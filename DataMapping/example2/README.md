# Example 2 — VA CPRS Primary Care Progress Note

**Document Hydration: Medical Clinical Note Use Case**

> Automated extraction of structured clinical data from physician dictations, transcripts, and free-text notes — mapped to a 75-field VA CPRS Primary Care Progress Note template with confidence scoring, clinical safety flags, and mandatory provider review.

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Use Case](#use-case)
- [Solution Architecture](#solution-architecture)
- [File Inventory](#file-inventory)
- [Schema Overview](#schema-overview)
- [Source Data Design](#source-data-design)
- [Expected Mapping Results](#expected-mapping-results)
- [Clinical Safety Considerations](#clinical-safety-considerations)
- [How to Run End-to-End](#how-to-run-end-to-end)
- [How to Extend](#how-to-extend)
- [Comparison with Example 1](#comparison-with-example-1)

---

## Problem Statement

VA providers spend significant time transcribing clinical encounter data from dictations, transcripts, and notes into structured CPRS progress note templates. The CPRS Primary Care Progress Note contains **75 fields** across 8 sections — including patient demographics, vitals, subjective/objective findings, mental status exam, suicide risk assessment, diagnoses with ICD-10 codes, treatment plans, and attestation.

Manual entry is:
- **Time-consuming** — a single progress note can take 15–30 minutes to complete in CPRS.
- **Error-prone** — ICD-10 codes, vital signs, and medication details are frequently mis-entered.
- **Inconsistent** — different providers document the same findings in different ways.
- **Audit-risky** — incomplete risk assessments or missing required fields can create compliance gaps.

This solution applies the same **document hydration pattern** from Example 1 (Enterprise Service Request) to a **clinical medical note** — demonstrating that the architecture is domain-agnostic.

---

## Use Case

**Semi-automated clinical note completion.**

A provider dictates or types a free-form clinical narrative after a patient encounter. The mapping engine:

1. Extracts structured field values from the unstructured narrative.
2. Normalizes clinical data (vitals, dates, ICD-10 codes, enums).
3. Maps values to the 75-field CPRS schema with confidence scores.
4. Flags all risk assessment and diagnosis fields for **mandatory clinician review**.
5. Produces a completed progress note ready for provider attestation.

**Critical distinction from Example 1:** Clinical notes require **mandatory provider review** for patient safety. The system assists documentation — it does not replace clinical judgment.

---

## Solution Architecture

```
┌─────────────────────────────────────────────────────────────┐
│              UNSTRUCTURED CLINICAL SOURCE DATA                │
│    (dictation, transcript, prior note, intake form, OCR)     │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   CLINICAL MAPPING ENGINE                     │
│                                                              │
│  1. Preprocess & normalize clinical text                     │
│  2. Detect SOAP sections, labeled vitals, med lists          │
│  3. Expand medical abbreviations for context inference        │
│  4. Match candidates to 75 schema fields                     │
│  5. Score confidence per field (0.0 – 1.0)                   │
│  6. Normalize: vitals, dates, ICD-10, NPI, enums             │
│  7. Validate against schema constraints                      │
│  8. Flag clinical safety items (risk, diagnoses)             │
│                                                              │
│  References: schema.json · DataMappingSpecification.md       │
│              CPRS_PCP_ProgressNote_Template.docx              │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   MAPPED JSON OUTPUT                          │
│     { "PATIENT_NAME": "...", "VITALS_BP": "142/88", ... }    │
└──────────┬──────────────────────────────────┬───────────────┘
           │                                  │
           ▼                                  ▼
┌─────────────────────────┐   ┌───────────────────────────────┐
│   COMPLETED CPRS NOTE    │   │       MAPPING REPORT          │
│                          │   │                               │
│  Filled-in progress note │   │  Per-field confidence scores  │
│  ready for provider      │   │  Clinical safety flags        │
│  attestation in CPRS     │   │  Items for clinician review   │
│                          │   │  Missing required fields      │
└──────────────────────────┘   │  Overall score & letter grade │
                               └───────────────────────────────┘
```

---

## File Inventory

| File | Location | Purpose |
|---|---|---|
| `CPRS_PCP_ProgressNote_Template.docx` | `forms/` | Original VA CPRS Primary Care Progress Note template (legacy `.doc` format). 8 sections, 75 fields with checkboxes, tables, and narrative areas. |
| `schema.json` | `artifacts/` | Authoritative field definition: 75 fields with types, constraints, regex patterns, enum values, and examples. 50 required, 25 optional. |
| `DataMappingSpecification.md` | `artifacts/` | Complete mapping strategy: clinical keyword banks, SOAP section inference, normalization rules (vitals, ICD-10, NPI), confidence scoring, clinical safety escalation, and 100-point grading model. |
| `sample-input-1.txt` | `source-data/` | Realistic physician dictation (Dr. Webb, patient Thomas Ramirez) — covers ~80% of schema fields, deliberately omits ~20% to test unmapped handling. |
| `README.md` | `./` | This file. |

---

## Schema Overview

**75 total fields** across 8 sections:

| Section | Fields | Required | Optional |
|---|---|---|---|
| Patient & Encounter Information | 12 | 8 | 4 |
| Vitals | 10 | 8 | 2 |
| Subjective | 10 | 4 | 6 |
| Mental Status Examination (MSE) | 14 | 13 | 1 |
| Risk Assessment | 13 | 7 | 6 |
| Objective / Physical Examination | 3 | 2 | 1 |
| Assessment & Plan with ICD-10 | 8 | 4 | 4 |
| Attestation & Signature | 5 | 4 | 1 |
| **Total** | **75** | **50** | **25** |

Key field types:
- **Enums** — Visit Type, Medication Reconciliation, MSE fields (Appearance, Behavior, Affect, etc.), Risk levels, Follow-Up intervals, Note Status
- **ICD-10 array** — Up to 5 diagnoses with code, description, status, and service-connection
- **Vital signs** — BP (systolic/diastolic), Pulse, Temp, SpO2, RR, Weight, Height, BMI, Pain Scale
- **Free-text narratives** — HPI, ROS, Physical Exam, Clinical Impression, Plan
- **Provider identifiers** — NPI (10-digit), signatures, dates

---

## Source Data Design

The sample input (`source-data/sample-input-1.txt`) is a realistic physician dictation designed to:

| Design Goal | How Achieved |
|---|---|
| **~80% mappable** | ~60 of 75 fields can be extracted from the text |
| **~20% unmapped** | ~15 fields deliberately omitted (all optional) |
| **Normalization required** | Date "March 14, 2026" → 2026-03-14; Time "ten fifteen" → 10:15; Pain "6 out of 10" → 6 |
| **Informal clinical language** | Abbreviations (HTN, DM2, BID, PRN, NAD, CTA, RRR), colloquial phrasing |
| **4 ICD-10 diagnoses** | E11.65, I10, M17.11, E11.42 — tests array extraction |
| **Risk screening present** | All SI/HI denied, risk factors noted, overall LOW — tests safety flag logic |
| **B-grade target** | Enough mapped to score 80–89 points |

**Deliberately omitted fields** (to test unmapped handling):
- `RESIDENT_STUDENT`, `SUPERVISING_PROVIDER` — no trainee involved
- `VITALS_HEIGHT`, `VITALS_BMI` — not mentioned in dictation
- `MEDICATION_CHANGES` — changes in plan, not in dedicated changes field
- `SOCIAL_HISTORY_STATUS` — status enum not explicitly stated
- `MSE_NARRATIVE` — no additional MSE narrative beyond structured findings
- `RISK_SI_HI_DETAILS` — no SI/HI present, so no details
- `RISK_NARRATIVE` — no separate risk justification narrative
- `SAFETY_PLAN` — not explicitly addressed
- `RETURN_VISIT_INSTRUCTIONS` — partially embedded in plan, not labeled
- `COSIGN_SUPERVISOR`, `COSIGN_DATE_TIME` — no cosign needed

---

## Expected Mapping Results

| Metric | Expected Value |
|---|---|
| Total Fields | 75 |
| Mapped | ~60 |
| Unmapped | ~15 (all optional) |
| Required Fields Mapped | 50 / 50 |
| Score | ~82–86 / 100 |
| Grade | **B** |
| Clinical Safety Flags | Risk fields always flagged for review; ICD-10 always flagged |
| Constraint Violations | 1–2 expected (time format normalization, approximate weight) |

---

## Clinical Safety Considerations

This example introduces **clinical safety requirements** not present in Example 1:

1. **Risk assessment fields always require clinician review** — even when mapped with high confidence and all items are "Denies." This is a patient safety invariant.

2. **ICD-10 diagnoses always require clinician confirmation** — incorrect codes can affect treatment, billing, and service-connection determinations.

3. **Vital sign outliers are flagged** — BP 156/94 is elevated and warrants clinical documentation of whether it was addressed.

4. **Medication changes require verification** — new medications (empagliflozin, chlorthalidone, gabapentin) must be confirmed by the prescribing provider.

5. **The system never auto-finalizes a clinical note** — the note must be reviewed and signed by the provider in CPRS.

---

## How to Run End-to-End

1. **Review reference files:**
   - `forms/CPRS_PCP_ProgressNote_Template.docx` — the visual template
   - `artifacts/schema.json` — the field definitions
   - `artifacts/DataMappingSpecification.md` — the mapping rules

2. **Provide source data:**
   - `source-data/sample-input-1.txt` (or your own clinical note)

3. **Execute the mapping prompt** (when available in `prompts/`):
   - The AI agent reads all references + source data
   - Produces outputs in the `output/` folder

4. **Review outputs:**
   - `mapped-output.json` — all 75 keys with values or null
   - `mapping-report.md` — per-field detail, clinical safety flags, score, grade
   - `MappedOutput.docx` — completed progress note

5. **Clinician review:**
   - Address all flagged items (risk assessment, diagnoses, medication changes)
   - Confirm or correct mapped values
   - Sign the note in CPRS

---

## How to Extend

### Adapting to Other Note Types

The same pattern works for other VA note templates:

| Note Type | Adaptation Required |
|---|---|
| Mental Health Progress Note | Expand MSE section, add treatment modality fields |
| Urgent Care Note | Add triage/acuity fields, disposition |
| Discharge Summary | Add admission/discharge dates, discharge meds, follow-up |
| Nursing Assessment | Add nursing-specific fields (ADLs, fall risk, skin assessment) |
| Pre-Operative Evaluation | Add surgical history, anesthesia risk, clearance fields |

For each: create a new template `.docx`, update `schema.json` with the new field set, and update keyword banks in `DataMappingSpecification.md`.

### Adding Fields

1. Add the field to `schema.json` with full definition.
2. Add the placeholder to the template if generating a new `.docx`.
3. Add keywords to the mapping spec's heuristic tables.
4. Recalculate point allocation to maintain 100-point total.
5. Verify parity: template fields = schema fields.

---

## Comparison with Example 1

| Dimension | Example 1 (Enterprise Service Request) | Example 2 (VA CPRS Progress Note) |
|---|---|---|
| **Domain** | IT / Business | Healthcare / Clinical |
| **Fields** | 42 | 75 |
| **Source data** | Casual email | Physician dictation |
| **Clinical safety** | N/A | Mandatory — risk, diagnoses, medications |
| **Auto-finalization** | Allowed after review | Never — requires provider attestation |
| **ICD-10 codes** | N/A | Required with validation pattern |
| **Mental status exam** | N/A | 14 fields with enum constraints |
| **Risk assessment** | N/A | 13 fields with safety escalation |
| **Regulatory context** | SOC 2, data residency | VA/VHA clinical documentation standards |
| **Human review** | Optional for high confidence | Mandatory for all risk and diagnosis fields |

This demonstrates that the document hydration pattern is **domain-agnostic** — the same architecture (template + schema + mapping spec + execution prompt) scales from business forms to clinical documentation.

---

## Version History

| Version | Date | Description |
|---|---|---|
| 1.0.0 | 2026-03-17 | Initial release — 75 fields, 8 sections, clinical safety model, sample dictation |

---

*Generated as part of the Document Hydration Solution. This is a demonstration example using synthetic patient data. No real patient information is contained in this repository.*
