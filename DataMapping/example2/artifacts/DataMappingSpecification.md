# Data Mapping Specification — VA CPRS Primary Care Progress Note

**Version:** 1.0.0  
**Date:** 2026-03-17  
**Schema Reference:** `schema.json` v1.0.0  
**Template Reference:** `CPRS_PCP_ProgressNote_Template.docx`  

---

## Table of Contents

1. [Overview](#1-overview)
2. [Mapping Strategy](#2-mapping-strategy)
3. [Mapping Heuristics](#3-mapping-heuristics)
4. [Failure Handling](#4-failure-handling)
5. [Mapping Report & Grading](#5-mapping-report--grading)
6. [Worked Example](#6-worked-example)

---

## 1. Overview

This specification defines how **unstructured clinical source data** — such as physician dictations, EHR copy-forward notes, handwritten transcriptions, telehealth transcripts, or SOAP-format narratives — is interpreted, normalized, and mapped into the **75 structured fields** defined in `schema.json`. The resulting mapped values are then hydrated into the CPRS Primary Care Progress Note template.

The goal is **maximum automation with mandatory human clinical review**. Every mapping decision produces a confidence score, and the overall quality of a mapping pass is expressed as a letter grade derived from a 100-point scoring model.

**Clinical safety note:** This tool assists with documentation. It does **not** replace clinical judgment. All mapped values — especially risk assessment, diagnoses, and medication fields — must be reviewed and attested by a licensed provider before the note is finalized in CPRS.

---

## 2. Mapping Strategy

### 2.1 Source Data Interpretation

Source data may arrive in any of the following formats:

| Source Type | Examples |
|---|---|
| Dictation transcript | Dragon Medical, Nuance DAX, voice-to-text |
| EHR copy-forward | Previous CPRS note, problem list export |
| Handwritten transcription | Scanned clinic note, OCR output |
| Telehealth transcript | MS Teams, VA Video Connect transcript |
| SOAP-format narrative | Typed free-text clinical narrative |
| Structured intake form | Pre-visit questionnaire, kiosk check-in |

The mapping engine treats all source data as a **single normalized text corpus** after the following preprocessing steps:

1. **Encoding normalization** — Convert to UTF-8.
2. **Whitespace normalization** — Collapse multiple spaces, tabs, and line breaks into single spaces; preserve paragraph boundaries.
3. **Character cleanup** — Replace smart quotes with straight quotes, normalize dashes (em-dash → hyphen), strip invisible Unicode characters.
4. **Section detection** — Identify clinical document structure (S/O/A/P headings, labeled vital signs, medication lists, diagnosis blocks) to infer context boundaries.
5. **Medical abbreviation expansion** — Recognize common clinical abbreviations (HTN, DM, LBP, RRR, CTA, NAD, etc.) for context inference while preserving them in output.

### 2.2 Field Inference Strategy

Each of the 75 schema fields is matched against the source corpus using a **multi-signal approach**:

| Signal | Weight | Description |
|---|---|---|
| Exact label match | 0.40 | Source contains the field's label verbatim (e.g., "BP: 128/82") |
| Clinical synonym match | 0.25 | Source contains a medical synonym (e.g., "blood pressure" → VITALS_BP, "SI" → passive/active ideation) |
| Contextual proximity | 0.20 | A candidate value appears within the expected SOAP section or near a recognized clinical label |
| Pattern match | 0.15 | Value matches the field's structural type (e.g., ICD-10 pattern, BP format, NPI 10-digit) |

The weighted sum of active signals produces the **raw confidence score** for each candidate mapping.

### 2.3 Normalization Rules

All extracted values are normalized before insertion:

| Data Type | Normalization Rule |
|---|---|
| **Patient names** | Last, First M. format. Preserve hyphens and apostrophes. Title Case. |
| **Dates** | Convert to ISO 8601 (`YYYY-MM-DD`). Input formats: `MM/DD/YYYY`, `DD-Mon-YYYY`, `Month DD, YYYY`, and relative references resolved to absolute dates. |
| **Times** | Convert to 24-hour format (`HH:MM`). Input: "9:30 AM" → "09:30", "1430" → "14:30". |
| **Vital signs** | BP: `systolic/diastolic` (integers). Temp: numeric with one decimal. Pulse/RR/SpO2: integers. Weight: numeric. |
| **ICD-10 codes** | Uppercase letter + digits + optional decimal. Validate against pattern `^[A-Z][0-9]{2}(\.[0-9A-Z]{1,4})?$`. Strip spaces. |
| **NPI** | Exactly 10 digits. Strip hyphens or spaces. Reject non-numeric. |
| **SSN Last 4** | Exactly 4 digits. Strip any formatting. |
| **Enum fields** | Fuzzy-match against `enumValues` list. Accept if Levenshtein distance ≤ 2 or cosine similarity ≥ 0.85. Otherwise flag for review. |
| **Risk assessment enums** | Map synonyms: "denies SI" → "Denies", "endorses passive SI" → "Present". Always escalate ambiguity. |
| **Boolean-like** | Map `yes/no`, `positive/negative`, `denies/endorses`, `present/absent` → appropriate enum value. |
| **Free-text (long)** | Preserve original clinical formatting. Trim whitespace. Truncate to `maxLength` with `[TRUNCATED]` marker if exceeded. |
| **Pain scale** | Parse integer 0–10. Reject non-numeric. "rates pain as 3/10" → 3. |
| **IDs** | Uppercase. Strip leading/trailing whitespace. Preserve hyphens. |

---

## 3. Mapping Heuristics

### 3.1 Keyword and Phrase Detection

For each schema field, a **keyword bank** is maintained:

| Schema Field | Primary Keywords | Secondary Keywords / Phrases |
|---|---|---|
| `PATIENT_NAME` | "patient name", "name" | "veteran", "pt", "patient" |
| `DATE_OF_BIRTH` | "date of birth", "DOB" | "born", "birthday", "age" (calculate backward) |
| `VA_PATIENT_ID` | "patient id", "VA ID" | "MRN", "record number", "patient number" |
| `SSN_LAST_4` | "SSN", "last 4", "last four" | "social", "social security" |
| `ATTENDING_PROVIDER` | "attending", "provider" | "doc", "physician", "clinician", "MD", "DO" |
| `CLINIC_LOCATION` | "clinic", "location" | "facility", "site", "station" |
| `VITALS_BP` | "BP", "blood pressure" | "systolic", "diastolic", "mmHg" |
| `VITALS_PULSE` | "pulse", "heart rate", "HR" | "bpm", "beats per minute" |
| `VITALS_TEMP` | "temp", "temperature" | "°F", "degrees", "febrile", "afebrile" |
| `VITALS_SPO2` | "SpO2", "O2 sat", "oxygen sat" | "pulse ox", "saturation" |
| `VITALS_WEIGHT` | "weight", "wt" | "lbs", "pounds", "kg" |
| `CHIEF_COMPLAINT` | "chief complaint", "CC" | "reason for visit", "presenting complaint", "here for" |
| `HISTORY_PRESENT_ILLNESS` | "HPI", "history of present illness" | "history", "presents with", "onset" |
| `REVIEW_OF_SYSTEMS` | "ROS", "review of systems" | "systems review", "denies", "endorses" |
| `MEDICATION_RECONCILIATION` | "med rec", "medication reconciliation" | "medication review", "medication list" |
| `MSE_APPEARANCE` | "appearance" | "grooming", "dressed", "well-groomed", "disheveled" |
| `MSE_BEHAVIOR` | "behavior" | "cooperative", "agitated", "guarded" |
| `MSE_MOOD` | "mood" | "patient reports mood as", "states mood is" |
| `MSE_AFFECT` | "affect" | "euthymic", "constricted", "flat", "labile" |
| `RISK_PASSIVE_IDEATION` | "passive SI", "passive ideation" | "wishes were dead", "passive thoughts" |
| `RISK_LEVEL` | "risk level", "overall risk" | "low risk", "moderate risk", "high risk", "imminent" |
| `ICD10_DIAGNOSES` | "ICD-10", "diagnosis", "assessment" | "problem list", "Dx", "impression" |
| `PLAN` | "plan", "treatment plan" | "orders", "medications", "follow-up plan" |
| `FOLLOW_UP` | "follow-up", "f/u", "return" | "RTC", "return to clinic", "weeks" |
| `NPI` | "NPI" | "national provider", "provider number" |
| `NOTE_STATUS` | "note status", "status" | "completed", "unsigned", "addendum" |

*(Keyword banks exist for all 75 fields; the above is a representative sample.)*

### 3.2 Clinical Section Inference

Clinical notes typically follow the **SOAP** structure. The mapping engine uses section awareness:

| Detected Section | Maps To Schema Sections |
|---|---|
| **Header / Demographics** | Patient & Encounter Information |
| **Vitals / Triage** | Vitals |
| **S (Subjective)** | Subjective (CC, HPI, ROS, Meds, Social Hx) |
| **Mental Status / MSE** | Mental Status Examination |
| **Risk / Safety** | Risk Assessment |
| **O (Objective)** | Objective / Physical Examination |
| **A (Assessment)** | Assessment & Plan — ICD-10 codes, Clinical Impression |
| **P (Plan)** | Assessment & Plan — Plan, Follow-up, Referrals, Orders |
| **Signature / Attestation** | Attestation & Signature |

### 3.3 Confidence Scoring

Each field mapping receives a confidence score from **0.0 to 1.0**:

| Score Range | Interpretation | Action |
|---|---|---|
| **0.90 – 1.00** | High confidence — exact or near-exact match | Auto-accept |
| **0.70 – 0.89** | Moderate confidence — strong inference but not exact | Auto-accept; flag for optional review |
| **0.50 – 0.69** | Low confidence — plausible but uncertain | Map tentatively; **require clinician review** |
| **0.25 – 0.49** | Very low confidence — weak signal | Do not map; present as candidate for clinician selection |
| **0.00 – 0.24** | No viable candidate found | Leave field unmapped; escalate as missing |

**Clinical override:** Risk assessment fields (all `RISK_*` keys) and `ICD10_DIAGNOSES` **always require clinician review** regardless of confidence score.

### 3.4 Conflict Resolution

When multiple candidate values compete for the same field:

1. **Highest confidence wins** — Select the candidate with the highest composite confidence score.
2. **Clinical recency** — If scores are tied (within ±0.05), prefer the most recent observation (e.g., today's vitals over last visit's).
3. **Source authority** — Prefer structured data (e.g., triage vitals) over narrative mentions.
4. **Clinician tiebreaker** — If all automated tiebreakers fail, flag the field for clinician review with all candidates presented.

---

## 4. Failure Handling

### 4.1 Field Cannot Be Mapped

| Attribute | Value |
|---|---|
| **Reason** | No candidate value found in the source data. |
| **Impact** | If `required: true`, the note is considered incomplete. |
| **Resolution** | Present the unmapped field to the clinician. |
| **Clinician Review** | **Mandatory** for required fields. **Recommended** for optional fields. |

### 4.2 Value Violates Schema Constraints

| Attribute | Value |
|---|---|
| **Reason** | Candidate value fails validation (wrong format, out of range, not in enum). |
| **Impact** | Value cannot be inserted as-is. |
| **Resolution** | Attempt auto-correction: normalize format, fuzzy-match enum, truncate to maxLength. If correction fails, flag for clinician. |
| **Clinician Review** | **Mandatory** if auto-correction fails. |

### 4.3 Source Data Is Ambiguous

| Attribute | Value |
|---|---|
| **Reason** | Conflicting information (e.g., two different BP readings, contradictory risk statements). |
| **Impact** | Confidence is split; no single candidate exceeds auto-accept threshold. |
| **Resolution** | Present all candidates to clinician with individual confidence scores and source context. |
| **Clinician Review** | **Mandatory.** Clinical ambiguity must never be auto-resolved. |

### 4.4 Required Fields Are Missing

| Attribute | Value |
|---|---|
| **Reason** | One or more required fields have no mapped value. |
| **Impact** | Note is **not complete**. Cannot be signed in CPRS. |
| **Resolution** | Generate a Missing Required Fields list. Route to clinician. |
| **Clinician Review** | **Mandatory.** Note cannot be finalized. |

### 4.5 Clinical Safety Escalation

| Attribute | Value |
|---|---|
| **Reason** | Risk assessment indicates present SI/HI, or risk level is High/Imminent, or ICD-10 codes indicate acute conditions. |
| **Impact** | Requires immediate clinical attention beyond documentation. |
| **Resolution** | Flag prominently in mapping report. **Do not auto-accept.** Alert clinician that clinical action may be required beyond documentation. |
| **Clinician Review** | **Mandatory and urgent.** |

---

## 5. Mapping Report & Grading

### 5.1 Scoring Model

The mapping quality is scored on a **100-point scale**.

#### Point Allocation

The schema contains **75 fields**: **50 required** and **25 optional**.

| Category | Fields | Points per Field | Max Points |
|---|---|---|---|
| Required fields | 50 | 1.50 | 75.00 |
| Optional fields | 25 | 1.00 | 25.00 |
| **Total** | **75** | — | **100.00** |

#### Per-Field Scoring

Each field earns points based on its mapping confidence:

| Confidence Range | Points Earned (% of field max) | Description |
|---|---|---|
| 0.90 – 1.00 | 100% | Full credit |
| 0.70 – 0.89 | 80% | Strong mapping, minor deduction |
| 0.50 – 0.69 | 50% | Partial credit; clinician review recommended |
| 0.25 – 0.49 | 20% | Minimal credit; value likely needs correction |
| 0.00 – 0.24 | 0% | No credit; field unmapped |

#### Example Calculation

A required field (`PATIENT_NAME`, 1.50 max points) mapped with confidence 0.95:

> Points earned = 1.50 × 100% = **1.50 points**

An optional field (`PAIN_LOCATION`, 1.00 max points) mapped with confidence 0.75:

> Points earned = 1.00 × 80% = **0.80 points**

### 5.2 Grade Bands

| Grade | Score Range | Interpretation |
|---|---|---|
| **A** | 90.0 – 100.0 | Excellent — note is ready for clinician attestation with minimal corrections. |
| **B** | 80.0 – 89.9 | Good — most fields mapped accurately; a few may need review. |
| **C** | 70.0 – 79.9 | Acceptable — note is usable but several fields need attention. |
| **D** | 60.0 – 69.9 | Below average — significant gaps; clinician must complete manually. |
| **F** | 0.0 – 59.9 | Failing — source data insufficient for this note template. |

### 5.3 Sample Mapping Report Structure

```
═══════════════════════════════════════════════════════════════
       CPRS PROGRESS NOTE — MAPPING REPORT
═══════════════════════════════════════════════════════════════

Report ID:          RPT-2026-03-17-00001
Generated:          2026-03-17T10:00:00Z
Schema Version:     1.0.0
Template:           CPRS_PCP_ProgressNote_Template.docx
Source Document:    clinical_note_source.txt

───────────────────────────────────────────────────────────────
  SUMMARY
───────────────────────────────────────────────────────────────

Total Fields:               75
Mapped (High Confidence):   40
Mapped (Moderate):          12
Mapped (Low):                2
Unmapped:                   14
Constraint Violations:       1 (auto-corrected)

Score:                      81.5 / 100.0
Grade:                      B

⚠️  CLINICAL SAFETY FLAGS:  None

───────────────────────────────────────────────────────────────
  FIELD-BY-FIELD DETAIL
───────────────────────────────────────────────────────────────

  Section: Patient & Encounter Information
  ─────────────────────────────────────────

  PATIENT_NAME
    Mapped Value:    Johnson, Robert A.
    Confidence:      0.95
    Points:          1.70 / 1.70
    Source Signal:   Exact label match ("Patient: Robert A. Johnson")
    Status:          ✅ AUTO-ACCEPTED

  ... (remaining fields follow same format) ...

───────────────────────────────────────────────────────────────
  CLINICAL SAFETY FLAGS
───────────────────────────────────────────────────────────────

  (None — all risk fields indicate Low risk, SI/HI denied.)

───────────────────────────────────────────────────────────────
  ISSUES REQUIRING CLINICIAN REVIEW
───────────────────────────────────────────────────────────────

  1. VITALS_HEIGHT
     Reason:      Not found in source data.
     Action:      Clinician should enter from chart.

  ... (additional issues) ...

───────────────────────────────────────────────────────────────
  MISSING REQUIRED FIELDS
───────────────────────────────────────────────────────────────

  None — all 50 required fields have mapped values.  ✅

═══════════════════════════════════════════════════════════════
                        END OF REPORT
═══════════════════════════════════════════════════════════════
```

### 5.4 Score Calculation Walkthrough

1. For each field, look up its **maximum points** (1.50 for required, 1.00 for optional).
2. Determine the **confidence range** of the mapping.
3. Multiply maximum points by the **percentage** for that confidence range.
4. Sum all 75 field scores.
5. Look up the **grade band** for the total.

---

## 6. Worked Example

### 6.1 Sample Unstructured Input

```
Dictation — Dr. Sarah Chen — 03/17/2026 at 0930

Patient Robert Johnson, DOB 04/15/1968 (he's 57), VA ID 10042857,
last four 7832. Seen today in PC Clinic 3B at the Puget Sound VA.
This is an in-person follow-up.

Vitals from triage: BP 142/88, pulse 82, temp 98.4, O2 sat 97%,
resp rate 18, weight 198 lbs. Pain 4 out of 10, lower back.

CC: Follow-up HTN and chronic LBP.

HPI: 57 y/o male veteran with hx of HTN x 10 years and chronic
low back pain s/p lumbar strain during service. Currently on
lisinopril 10mg daily and meloxicam 15mg PRN. Reports BP has
been "running high" per home readings (150s/90s). Back pain is
unchanged, rates it 4/10 today, worse with prolonged standing.
Denies chest pain, SOB, headache, visual changes.

ROS: Constitutional - no fever, no weight loss. CV - no chest pain,
no palpitations. Resp - no SOB, no cough. MSK - chronic LBP as
above, no joint swelling. Neuro - no numbness/tingling.

Meds reviewed, no changes. Social hx - former smoker, quit 2019,
social drinker 2-3 per week, denies substances, light exercise
walks 3x/week.

MSE: Well-groomed, cooperative, good eye contact, normal speech,
says he feels "okay," affect euthymic, thought process linear
and goal-directed, no delusions, no hallucinations, alert and
oriented x4, insight good, judgment good.

Risk: Denies all SI, HI, and self-harm. No prior attempts.
Protective factors include wife and church community.
Risk level LOW. Safety plan N/A. Crisis line already known.

Exam: Well-nourished male, NAD. CV: RRR, no murmurs. Lungs CTA
bilaterally. MSK: Lumbar paraspinal tenderness to palpation,
no radiculopathy, ROM limited by pain. Neuro: intact sensation
and strength in lower extremities.

Recent labs (03/10/2026): BMP normal, Cr 1.0, GFR >60. A1c 6.1.
LDL 118.

Assessment:
1. I10 — Essential hypertension, chronic, SC-connected — not at
   goal, increase lisinopril
2. M54.5 — Low back pain, chronic, SC-connected — stable, continue
   current management

Impression: 57 y/o veteran with SC HTN not at goal and chronic
stable LBP. Will intensify antihypertensive therapy.

Plan:
1. HTN: Increase lisinopril from 10mg to 20mg daily. Recheck BP
   in 4 weeks. Continue low sodium diet counseling.
2. LBP: Continue meloxicam 15mg PRN. Refer to PT for core
   strengthening. Home exercise program reviewed.

Follow-up 4 weeks. Return sooner if BP >160/100 or new symptoms.
Referrals: Physical Therapy. Orders: BMP in 4 weeks, lisinopril
20mg daily renewal.

Signed: Sarah M. Chen, MD / NPI 1234567890 / 03/17/2026 1045
Note status: Completed
```

### 6.2 Resulting Mapped JSON

```json
{
  "PATIENT_NAME": "Johnson, Robert A.",
  "DATE_OF_BIRTH": "1968-04-15",
  "VA_PATIENT_ID": "VA-10042857",
  "SSN_LAST_4": "7832",
  "DATE_OF_VISIT": "2026-03-17",
  "TIME_OF_VISIT": "09:30",
  "ATTENDING_PROVIDER": "Dr. Sarah M. Chen, MD",
  "CLINIC_LOCATION": "Primary Care Clinic 3B, VA Puget Sound HCS",
  "RESIDENT_STUDENT": null,
  "SUPERVISING_PROVIDER": null,
  "VISIT_TYPE": "In-Person",
  "INTERPRETER": null,
  "VITALS_BP": "142/88",
  "VITALS_PULSE": 82,
  "VITALS_TEMP": "98.4",
  "VITALS_SPO2": 97,
  "VITALS_RESP_RATE": 18,
  "VITALS_WEIGHT": "198",
  "VITALS_HEIGHT": null,
  "VITALS_BMI": null,
  "PAIN_SCALE": 4,
  "PAIN_LOCATION": "Lower back",
  "CHIEF_COMPLAINT": "Follow-up for hypertension management and chronic low back pain.",
  "HISTORY_PRESENT_ILLNESS": "57 y/o male veteran with hx of HTN x 10 years and chronic low back pain s/p lumbar strain during service...",
  "REVIEW_OF_SYSTEMS": "Constitutional: no fever, no weight loss. CV: no chest pain, no palpitations...",
  "MEDICATION_RECONCILIATION": "Reviewed — No Changes",
  "MEDICATION_CHANGES": null,
  "SOCIAL_HISTORY_STATUS": null,
  "TOBACCO_STATUS": "Former",
  "ALCOHOL_STATUS": "Social — 2-3 drinks/week",
  "SUBSTANCE_STATUS": "Denies",
  "EXERCISE_STATUS": "Light",
  "MSE_APPEARANCE": "Well-groomed",
  "MSE_BEHAVIOR": "Cooperative",
  "MSE_EYE_CONTACT": "Normal",
  "MSE_SPEECH": "Normal rate/tone/volume",
  "MSE_MOOD": "\"Okay\"",
  "MSE_AFFECT": "Euthymic",
  "MSE_THOUGHT_PROCESS": "Linear / Goal-directed",
  "MSE_THOUGHT_CONTENT": "No delusions. Denies obsessive thoughts.",
  "MSE_PERCEPTIONS": "No hallucinations",
  "MSE_COGNITION": "Alert & Oriented x4",
  "MSE_INSIGHT": "Good",
  "MSE_JUDGMENT": "Good",
  "MSE_NARRATIVE": null,
  "RISK_PASSIVE_IDEATION": "Denies",
  "RISK_ACTIVE_IDEATION_NO_PLAN": "Denies",
  "RISK_ACTIVE_IDEATION_WITH_PLAN": "Denies",
  "RISK_ACTIVE_IDEATION_WITH_INTENT": "Denies",
  "RISK_HOMICIDAL_IDEATION": "Denies",
  "RISK_SELF_HARM": "Denies",
  "RISK_SI_HI_DETAILS": null,
  "RISK_FACTORS": null,
  "PROTECTIVE_FACTORS": "Social support (wife), Religious beliefs (church community)",
  "RISK_LEVEL": "Low",
  "RISK_NARRATIVE": null,
  "SAFETY_PLAN": "N/A",
  "CRISIS_LINE_PROVIDED": "Already known to patient",
  "OBJECTIVE_GENERAL": "Well-nourished male, no acute distress. Alert and oriented.",
  "OBJECTIVE_SYSTEM_EXAM": "CV: RRR, no murmurs. Lungs: CTA bilaterally. MSK: Lumbar paraspinal tenderness to palpation, no radiculopathy, ROM limited by pain. Neuro: intact sensation and strength in lower extremities.",
  "OBJECTIVE_LAB_IMAGING": "BMP (03/10/2026): normal, Cr 1.0, GFR >60. A1c 6.1%. LDL 118.",
  "ICD10_DIAGNOSES": [
    { "code": "I10", "diagnosis": "Essential hypertension", "status": "Chronic", "serviceConnected": "Yes" },
    { "code": "M54.5", "diagnosis": "Low back pain", "status": "Chronic", "serviceConnected": "Yes" }
  ],
  "CLINICAL_IMPRESSION": "57 y/o veteran with service-connected HTN not at goal and chronic stable LBP. Will intensify antihypertensive therapy.",
  "PLAN": "1. HTN: Increase lisinopril from 10mg to 20mg daily. Recheck BP in 4 weeks. Continue low sodium diet counseling.\n2. LBP: Continue meloxicam 15mg PRN. Refer to PT for core strengthening. Home exercise program reviewed.",
  "FOLLOW_UP": "4 weeks",
  "RETURN_VISIT_INSTRUCTIONS": "Return in 4 weeks for BP recheck. Return sooner if BP >160/100 or new symptoms.",
  "REFERRALS_PLACED": "Physical Therapy",
  "ORDERS_ENTERED": "Labs (BMP in 4 weeks), Meds (lisinopril 20mg daily renewal)",
  "PROVIDER_SIGNATURE": "Sarah M. Chen, MD",
  "DATE_TIME_SIGNED": "2026-03-17 10:45",
  "PRINTED_NAME": "Sarah M. Chen, MD",
  "NPI": "1234567890",
  "COSIGN_SUPERVISOR": null,
  "COSIGN_DATE_TIME": null,
  "NOTE_STATUS": "Completed"
}
```

### 6.3 Example Mapping Report Summary

```
Total Fields:  75     Mapped: 60     Unmapped: 15
Required Mapped: 50/50 ✅    Score: 83.2 / 100    Grade: B

Unmapped (all optional): RESIDENT_STUDENT, SUPERVISING_PROVIDER,
INTERPRETER, VITALS_HEIGHT, VITALS_BMI, MEDICATION_CHANGES,
SOCIAL_HISTORY_STATUS, MSE_NARRATIVE, RISK_SI_HI_DETAILS,
RISK_FACTORS, RISK_NARRATIVE, COSIGN_SUPERVISOR, COSIGN_DATE_TIME

Issues for Review: 3
  1. VITALS_BP — elevated reading (142/88) may warrant clinical flag
  2. ICD10_DIAGNOSES — always requires clinician confirmation
  3. RISK_LEVEL — always requires clinician confirmation (even though Low)
```
