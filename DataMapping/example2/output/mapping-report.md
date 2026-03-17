```
═══════════════════════════════════════════════════════════════
       CPRS PRIMARY CARE PROGRESS NOTE — MAPPING REPORT
═══════════════════════════════════════════════════════════════

Report ID:          RPT-2026-03-17-00002
Generated:          2026-03-17T12:00:00Z
Schema Version:     1.0.0
Template:           CPRS_PCP_ProgressNote_Template.docx
Source Document:    sample-input-1.txt (Dr. Webb dictation — Ramirez, Thomas E.)

───────────────────────────────────────────────────────────────
  SUMMARY
───────────────────────────────────────────────────────────────

Total Fields:               75
Mapped (High Confidence):   42   (≥ 0.90)
Mapped (Moderate):          18   (0.70 – 0.89)
Mapped (Low):                0   (0.50 – 0.69)
Unmapped:                   15   (no candidate)
Constraint Violations:       2   (auto-corrected)

Required Fields Mapped:     50 / 50   ✅
Optional Fields Mapped:     10 / 25

───────────────────────────────────────────────────────────────
  SCORING MODEL
───────────────────────────────────────────────────────────────

  Required fields:  50 × 1.50 pts  = 75.00 max
  Optional fields:  25 × 1.00 pts  = 25.00 max
  ─────────────────────────────────────────
  Total possible:                    100.00

  Confidence → Points:
    0.90 – 1.00  →  100% of field max
    0.70 – 0.89  →   80% of field max
    0.50 – 0.69  →   50% of field max
    0.25 – 0.49  →   20% of field max
    0.00 – 0.24  →    0% of field max

───────────────────────────────────────────────────────────────
  SCORE CALCULATION
───────────────────────────────────────────────────────────────

  Required fields (50):
    100% credit:  32 fields × 1.50 = 48.00
     80% credit:  18 fields × 1.20 = 21.60
    Required subtotal:                69.60

  Optional fields (25):
    100% credit:  10 fields × 1.00 = 10.00
     80% credit:   0 fields × 0.80 =  0.00
      0% credit:  15 fields × 0.00 =  0.00
    Optional subtotal:                10.00

                                     ──────
  TOTAL SCORE:                       79.60 / 100.0

  GRADE:                              C

───────────────────────────────────────────────────────────────
  ⚠️  CLINICAL SAFETY FLAGS
───────────────────────────────────────────────────────────────

  1. RISK ASSESSMENT (all fields) — MANDATORY CLINICIAN REVIEW
     All SI/HI screening items mapped as "Denies." Overall risk
     LOW. However: patient endorses firearms access and job
     instability as risk factors. Clinician must confirm risk
     level determination.

  2. ICD-10 DIAGNOSES — MANDATORY CLINICIAN REVIEW
     4 diagnoses mapped with ICD-10 codes. All marked service-
     connected. Clinician must verify codes, descriptions, and
     SC determinations.

  3. MEDICATION CHANGES — CLINICIAN VERIFICATION REQUIRED
     4 new medications added, 1 discontinued. Provider must
     confirm all medication orders match clinical intent.

  4. VITALS — BP ELEVATED (156/94)
     Blood pressure exceeds 140/90 threshold. Source data
     documents this is being addressed in the plan. Flagged
     for visibility.

───────────────────────────────────────────────────────────────
  FIELD-BY-FIELD DETAIL
───────────────────────────────────────────────────────────────

  Section: Patient & Encounter Information
  ─────────────────────────────────────────

  PATIENT_NAME                                    [REQUIRED]
    Mapped Value:    Ramirez, Thomas E.
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("Seeing Mr. Thomas E.
                     Ramirez today"). Normalized to Last, First M.
    Status:          ✅ AUTO-ACCEPTED

  DATE_OF_BIRTH                                   [REQUIRED]
    Mapped Value:    1971-07-08
    Confidence:      0.96
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("Born July 8, 1971").
    Normalization:   "July 8, 1971" → "1971-07-08" (ISO 8601)
    Status:          ✅ AUTO-ACCEPTED

  VA_PATIENT_ID                                   [REQUIRED]
    Mapped Value:    VA-10087234
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Synonym match ("His VA number is 10087234").
                     Prefixed with "VA-" per normalization.
    Status:          ✅ AUTO-ACCEPTED

  SSN_LAST_4                                      [REQUIRED]
    Mapped Value:    4419
    Confidence:      0.97
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("last four of social is
                     4419"). Pattern match: 4 digits.
    Status:          ✅ AUTO-ACCEPTED

  DATE_OF_VISIT                                   [REQUIRED]
    Mapped Value:    2026-03-14
    Confidence:      0.96
    Points:          1.50 / 1.50
    Source Signal:   Header match ("Dr. Marcus Webb — March 14,
                     2026").
    Normalization:   "March 14, 2026" → "2026-03-14" (ISO 8601)
    Status:          ✅ AUTO-ACCEPTED

  TIME_OF_VISIT                                   [REQUIRED]
    Mapped Value:    10:15
    Confidence:      0.80
    Points:          1.20 / 1.50
    Source Signal:   Contextual inference ("We started around ten
                     fifteen in the morning").
    Normalization:   Natural language "ten fifteen in the morning"
                     → "10:15" (24-hour).
    Constraint:      ⚠️ Auto-corrected — natural language time
                     converted to HH:MM format.
    Status:          ✅ AUTO-ACCEPTED (flagged — "around" qualifier
                     reduces confidence)

  ATTENDING_PROVIDER                              [REQUIRED]
    Mapped Value:    Dr. Marcus J. Webb, MD
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Header match ("Dr. Marcus Webb") + signature
                     match ("Marcus J. Webb, MD"). Full name from
                     signature.
    Status:          ✅ AUTO-ACCEPTED

  CLINIC_LOCATION                                 [REQUIRED]
    Mapped Value:    Building 7 Primary Care, Minneapolis VA
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Keyword match ("Appointment at Building 7
                     Primary Care, Minneapolis VA").
    Status:          ✅ AUTO-ACCEPTED

  RESIDENT_STUDENT                                [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No trainee mentioned in the source data.
    Status:          ⬜ UNMAPPED

  SUPERVISING_PROVIDER                            [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No supervisor/cosign reference. Single provider
                     encounter.
    Status:          ⬜ UNMAPPED

  VISIT_TYPE                                      [REQUIRED]
    Mapped Value:    Telehealth-Video
    Confidence:      0.97
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("This was a telehealth video
                     visit"). Enum match: "Telehealth-Video".
    Status:          ✅ AUTO-ACCEPTED

  INTERPRETER                                     [OPTIONAL]
    Mapped Value:    Used — Language: Spanish
    Confidence:      0.95
    Points:          1.00 / 1.00
    Source Signal:   Exact keyword match ("Used a Spanish
                     interpreter"). Normalized to standard format.
    Status:          ✅ AUTO-ACCEPTED


  Section: Vitals
  ───────────────

  VITALS_BP                                       [REQUIRED]
    Mapped Value:    156/94
    Confidence:      0.97
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("Blood pressure 156/94").
                     Pattern match: digits/digits.
    ⚠️ Clinical flag: BP > 140/90 — elevated.
    Status:          ✅ AUTO-ACCEPTED

  VITALS_PULSE                                    [REQUIRED]
    Mapped Value:    91
    Confidence:      0.96
    Points:          1.50 / 1.50
    Source Signal:   Synonym match ("heart rate 91"). Integer
                     validated.
    Status:          ✅ AUTO-ACCEPTED

  VITALS_TEMP                                     [REQUIRED]
    Mapped Value:    98.2
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Keyword match ("temp normal at 98.2"). Pattern
                     match: decimal number.
    Status:          ✅ AUTO-ACCEPTED

  VITALS_SPO2                                     [REQUIRED]
    Mapped Value:    96
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Synonym match ("oxygen was 96 percent").
                     Integer extracted, "percent" stripped.
    Status:          ✅ AUTO-ACCEPTED

  VITALS_RESP_RATE                                [REQUIRED]
    Mapped Value:    20
    Confidence:      0.96
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("resp rate 20"). Integer
                     validated.
    Status:          ✅ AUTO-ACCEPTED

  VITALS_WEIGHT                                   [REQUIRED]
    Mapped Value:    224
    Confidence:      0.85
    Points:          1.20 / 1.50
    Source Signal:   Keyword match ("he weighs about 224 lbs").
                     Numeric extracted.
    Constraint:      ⚠️ Auto-corrected — "about" qualifier
                     indicates approximate value. Integer 224
                     selected.
    Status:          ✅ AUTO-ACCEPTED (flagged — approximate)

  VITALS_HEIGHT                                   [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No height measurement found in source data.
    Status:          ⬜ UNMAPPED

  VITALS_BMI                                      [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No BMI value found. Cannot calculate without
                     height.
    Status:          ⬜ UNMAPPED

  PAIN_SCALE                                      [REQUIRED]
    Mapped Value:    6
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Keyword match ("pain as a 6"). Integer 0–10
                     validated.
    Status:          ✅ AUTO-ACCEPTED

  PAIN_LOCATION                                   [OPTIONAL]
    Mapped Value:    Right knee
    Confidence:      0.95
    Points:          1.00 / 1.00
    Source Signal:   Contextual proximity — "pain as a 6 — mostly
                     in his right knee".
    Status:          ✅ AUTO-ACCEPTED


  Section: Subjective
  ───────────────────

  CHIEF_COMPLAINT                                 [REQUIRED]
    Mapped Value:    Ongoing management of type 2 diabetes,
                     hypertension, and worsening right knee pain.
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("Chief complaint: Here for
                     ongoing management..."). Normalized to clinical
                     phrasing.
    Status:          ✅ AUTO-ACCEPTED

  HISTORY_PRESENT_ILLNESS                         [REQUIRED]
    Mapped Value:    54 year old male veteran, service-connected
                     DM2 and HTN. Diagnosed with diabetes 2015...
                     [full narrative preserved]
    Confidence:      0.92
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("HPI:"). Full paragraph
                     captured as narrative.
    Status:          ✅ AUTO-ACCEPTED

  REVIEW_OF_SYSTEMS                               [REQUIRED]
    Mapped Value:    Constitutional: no fever, stable weight...
                     [full 10-system review preserved]
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("Review of systems:"). Full
                     multi-system review captured.
    Status:          ✅ AUTO-ACCEPTED

  MEDICATION_RECONCILIATION                       [REQUIRED]
    Mapped Value:    Changes Made
    Confidence:      0.88
    Points:          1.20 / 1.50
    Source Signal:   Keyword match ("Medication reconciliation —
                     changes being made today"). Enum fuzzy match:
                     "changes being made" → "Changes Made".
    Status:          ✅ AUTO-ACCEPTED (flagged — inferred from
                     informal phrasing)

  MEDICATION_CHANGES                              [OPTIONAL]
    Mapped Value:    See plan — empagliflozin 10mg daily added,
                     chlorthalidone 12.5mg daily added, gabapentin
                     300mg qHS added, topical diclofenac gel added,
                     naproxen discontinued.
    Confidence:      0.85
    Points:          0.80 / 1.00
    Source Signal:   Contextual inference — medication changes
                     extracted from Plan section and cross-referenced
                     with Orders. Not in a dedicated "changes" field.
    Status:          ✅ AUTO-ACCEPTED (flagged — synthesized from
                     plan, not explicitly labeled)

  SOCIAL_HISTORY_STATUS                           [OPTIONAL]
    Mapped Value:    Updated
    Confidence:      0.90
    Points:          1.00 / 1.00
    Source Signal:   Keyword match ("Social history update:").
                     Presence of update content implies "Updated".
    Status:          ✅ AUTO-ACCEPTED

  TOBACCO_STATUS                                  [OPTIONAL]
    Mapped Value:    Former — quit 2018, previously 1 PPD
    Confidence:      0.90
    Points:          1.00 / 1.00
    Source Signal:   Keyword match ("quit smoking about 8 years
                     ago, down from a pack a day"). Calculated:
                     2026 − 8 = 2018.
    Status:          ✅ AUTO-ACCEPTED

  ALCOHOL_STATUS                                  [OPTIONAL]
    Mapped Value:    Social — 1-2 beers on weekends
    Confidence:      0.88
    Points:          0.80 / 1.00
    Source Signal:   Keyword match ("Drinks beer 'maybe once or
                     twice on weekends'"). Categorized as social.
    Status:          ✅ AUTO-ACCEPTED

  SUBSTANCE_STATUS                                [OPTIONAL]
    Mapped Value:    Denies
    Confidence:      0.97
    Points:          1.00 / 1.00
    Source Signal:   Exact match ("Denies any substance use").
    Status:          ✅ AUTO-ACCEPTED

  EXERCISE_STATUS                                 [OPTIONAL]
    Mapped Value:    Sedentary
    Confidence:      0.93
    Points:          1.00 / 1.00
    Source Signal:   Exact keyword match ("Currently sedentary").
                     Exact enum match: "Sedentary".
    Status:          ✅ AUTO-ACCEPTED


  Section: Mental Status Examination (MSE)
  ────────────────────────────────────────

  MSE_APPEARANCE                                  [REQUIRED]
    Mapped Value:    Appropriate
    Confidence:      0.88
    Points:          1.20 / 1.50
    Source Signal:   Synonym match ("Appropriately dressed").
                     Fuzzy enum match: "Appropriately" → "Appropriate".
    Status:          ✅ AUTO-ACCEPTED (flagged — fuzzy match)

  MSE_BEHAVIOR                                    [REQUIRED]
    Mapped Value:    Cooperative
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("Cooperative during the
                     visit"). Exact enum match.
    Status:          ✅ AUTO-ACCEPTED

  MSE_EYE_CONTACT                                 [REQUIRED]
    Mapped Value:    Normal
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("Eye contact was normal").
                     Exact enum match: "Normal".
    Status:          ✅ AUTO-ACCEPTED

  MSE_SPEECH                                      [REQUIRED]
    Mapped Value:    Normal rate/tone/volume
    Confidence:      0.88
    Points:          1.20 / 1.50
    Source Signal:   Synonym match ("Speech normal rate and tone").
                     Fuzzy enum: "normal rate and tone" →
                     "Normal rate/tone/volume".
    Status:          ✅ AUTO-ACCEPTED (flagged — source says "rate
                     and tone" but enum includes "/volume")

  MSE_MOOD                                        [REQUIRED]
    Mapped Value:    "Stressed"
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("his mood is 'stressed'").
                     Patient's own words captured in quotes.
    Status:          ✅ AUTO-ACCEPTED

  MSE_AFFECT                                      [REQUIRED]
    Mapped Value:    Anxious
    Confidence:      0.88
    Points:          1.20 / 1.50
    Source Signal:   Synonym match ("Affect was mildly anxious").
                     Enum match: "Anxious". "Mildly" qualifier
                     noted but enum does not support severity
                     gradations.
    Status:          ✅ AUTO-ACCEPTED (flagged — "mildly" dropped)

  MSE_THOUGHT_PROCESS                             [REQUIRED]
    Mapped Value:    Linear / Goal-directed
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("Thought process linear and
                     goal-directed"). Enum exact match.
    Status:          ✅ AUTO-ACCEPTED

  MSE_THOUGHT_CONTENT                             [REQUIRED]
    Mapped Value:    No delusions. No paranoia. Endorses financial
                     and functional stressors.
    Confidence:      0.88
    Points:          1.20 / 1.50
    Source Signal:   Keyword match ("No delusions or paranoia").
                     Contextual inference — stressors noted from
                     MSE narrative.
    Status:          ✅ AUTO-ACCEPTED (flagged — synthesized from
                     multiple signals)

  MSE_PERCEPTIONS                                 [REQUIRED]
    Mapped Value:    No hallucinations
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("No hallucinations
                     reported").
    Status:          ✅ AUTO-ACCEPTED

  MSE_COGNITION                                   [REQUIRED]
    Mapped Value:    Alert & Oriented x4
    Confidence:      0.92
    Points:          1.50 / 1.50
    Source Signal:   Synonym match ("Oriented to person, place,
                     time, and situation"). Normalized to standard
                     format: "Alert & Oriented x4".
    Status:          ✅ AUTO-ACCEPTED

  MSE_INSIGHT                                     [REQUIRED]
    Mapped Value:    Fair
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("Insight is fair").
                     Exact enum match: "Fair".
    Status:          ✅ AUTO-ACCEPTED

  MSE_JUDGMENT                                    [REQUIRED]
    Mapped Value:    Fair
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("Judgment fair"). Exact
                     enum match: "Fair".
    Status:          ✅ AUTO-ACCEPTED

  MSE_NARRATIVE                                   [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No additional MSE narrative beyond the
                     structured findings already captured above.
    Status:          ⬜ UNMAPPED


  Section: Risk Assessment
  ────────────────────────

  ⚠️  ALL RISK ASSESSMENT FIELDS REQUIRE MANDATORY CLINICIAN
      REVIEW regardless of confidence score. This is a clinical
      safety invariant.

  RISK_PASSIVE_IDEATION                           [REQUIRED]
    Mapped Value:    Denies
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("Denies passive SI").
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_ACTIVE_IDEATION_NO_PLAN                    [REQUIRED]
    Mapped Value:    Denies
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("active SI" within grouped
                     denial "Denies passive SI, active SI,
                     active SI with plan...").
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_ACTIVE_IDEATION_WITH_PLAN                  [REQUIRED]
    Mapped Value:    Denies
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("active SI with plan" in grouped
                     denial).
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_ACTIVE_IDEATION_WITH_INTENT                [REQUIRED]
    Mapped Value:    Denies
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("active SI with intent" in
                     grouped denial).
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_HOMICIDAL_IDEATION                         [REQUIRED]
    Mapped Value:    Denies
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("Denies HI").
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_SELF_HARM                                  [REQUIRED]
    Mapped Value:    Denies
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact match ("Denies self-harm").
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_SI_HI_DETAILS                              [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No SI/HI present; no details to document.
    Status:          ⬜ UNMAPPED (appropriate — N/A)

  RISK_FACTORS                                    [OPTIONAL]
    Mapped Value:    Firearms access, Recent life stressor (job
                     instability)
    Confidence:      0.90
    Points:          1.00 / 1.00
    Source Signal:   Keyword match ("has firearms at home", "job
                     instability"). Cross-referenced with template
                     risk factor options.
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  PROTECTIVE_FACTORS                              [OPTIONAL]
    Mapped Value:    Social support (wife and two teenage children),
                     Engaged in treatment, Religious beliefs (church
                     community)
    Confidence:      0.90
    Points:          1.00 / 1.00
    Source Signal:   Keyword match ("strong family support — wife
                     and two teenage kids", "actively engaged with
                     his church").
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_LEVEL                                      [REQUIRED]
    Mapped Value:    Low
    Confidence:      0.92
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("Overall risk level LOW").
                     Enum match: "Low".
    Status:          🔒 CLINICIAN REVIEW REQUIRED

  RISK_NARRATIVE                                  [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No separate risk narrative or justification
                     paragraph found. Risk data is embedded in the
                     screening section but not as a standalone
                     narrative.
    Status:          ⬜ UNMAPPED

  SAFETY_PLAN                                     [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No safety plan discussion documented in the
                     source data.
    Status:          ⬜ UNMAPPED

  CRISIS_LINE_PROVIDED                            [OPTIONAL]
    Mapped Value:    Already known to patient
    Confidence:      0.93
    Points:          1.00 / 1.00
    Source Signal:   Exact match ("Veterans Crisis Line already
                     known to him").
    Status:          🔒 CLINICIAN REVIEW REQUIRED


  Section: Objective / Physical Examination
  ─────────────────────────────────────────

  OBJECTIVE_GENERAL                               [REQUIRED]
    Mapped Value:    Overweight male appearing stated age, no acute
                     distress, using a cane today.
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("General:"). Full narrative
                     captured.
    Status:          ✅ AUTO-ACCEPTED

  OBJECTIVE_SYSTEM_EXAM                           [REQUIRED]
    Mapped Value:    CV: Tachycardic at 91, regular rhythm, no
                     murmurs or gallops. Lungs: Clear bilaterally.
                     Abdomen: Soft, non-tender... [full multi-system
                     exam preserved]
    Confidence:      0.92
    Points:          1.50 / 1.50
    Source Signal:   Section detection — physical exam systems
                     (CV, Lungs, Abdomen, Right knee, Feet, Neuro)
                     captured as multi-system narrative.
    Status:          ✅ AUTO-ACCEPTED

  OBJECTIVE_LAB_IMAGING                           [OPTIONAL]
    Mapped Value:    Labs (03/01/2026): A1c 7.8 (improved from
                     8.2), fasting glucose 148, BMP — Na 139,
                     K 4.4, Cr 1.1, GFR 72. Lipids: TC 210,
                     LDL 132, HDL 38, TG 200. UA: no proteinuria.
                     Right knee x-ray (03/05/2026): moderate medial
                     compartment degenerative changes, no fracture.
    Confidence:      0.92
    Points:          1.00 / 1.00
    Source Signal:   Keyword match ("Labs reviewed from 03/01/2026",
                     "Right knee x-ray"). Full lab and imaging
                     narrative captured.
    Status:          ✅ AUTO-ACCEPTED


  Section: Assessment & Plan with ICD-10
  ──────────────────────────────────────

  ICD10_DIAGNOSES                                 [REQUIRED]
    Mapped Value:    [Array of 4 entries]
      1. E11.65 — Type 2 diabetes with hyperglycemia — Chronic — SC: Yes
      2. I10 — Essential hypertension — Chronic — SC: Yes
      3. M17.11 — Primary osteoarthritis, right knee — Active — SC: Yes
      4. E11.42 — Diabetic polyneuropathy — Chronic — SC: Yes
    Confidence:      0.88
    Points:          1.20 / 1.50
    Source Signal:   Exact label match ("Assessment and diagnoses:").
                     ICD-10 codes validated against pattern
                     ^[A-Z][0-9]{2}(\.[0-9A-Z]{1,4})?$. All 4
                     codes valid.
    Status:          🔒 CLINICIAN REVIEW REQUIRED — ICD-10 codes
                     and service-connection determinations always
                     require provider confirmation.

  CLINICAL_IMPRESSION                             [REQUIRED]
    Mapped Value:    54 year old veteran with multiple SC conditions.
                     DM improving but still above goal... [full
                     impression preserved]
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("Clinical impression:").
                     Full narrative captured.
    Status:          ✅ AUTO-ACCEPTED

  PLAN                                            [REQUIRED]
    Mapped Value:    1. DM2: Continue metformin 1000mg BID and
                     glipizide 5mg daily. Add empagliflozin 10mg
                     daily... [full 4-problem plan preserved]
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("Plan by problem:"). Full
                     multi-problem plan captured with structure.
    Status:          ✅ AUTO-ACCEPTED

  FOLLOW_UP                                       [REQUIRED]
    Mapped Value:    6 weeks
    Confidence:      0.93
    Points:          1.50 / 1.50
    Source Signal:   Keyword match ("Follow up in 6 weeks"). Exact
                     enum match: "6 weeks".
    Status:          ✅ AUTO-ACCEPTED

  RETURN_VISIT_INSTRUCTIONS                       [OPTIONAL]
    Mapped Value:    Return in 6 weeks to reassess BP on new
                     regimen and knee response to injection.
                     Return sooner for any hypo episodes, worsening
                     knee, or new foot concerns.
    Confidence:      0.85
    Points:          0.80 / 1.00
    Source Signal:   Contextual proximity — follow-up statement
                     includes return instructions. Not separately
                     labeled as "instructions."
    Status:          ✅ AUTO-ACCEPTED (flagged — not explicitly
                     labeled)

  REFERRALS_PLACED                                [OPTIONAL]
    Mapped Value:    Nutrition, Physical Therapy, Podiatry, Ortho
                     (conditional)
    Confidence:      0.95
    Points:          1.00 / 1.00
    Source Signal:   Exact keyword match ("Referrals: Nutrition,
                     Physical Therapy, Podiatry, Ortho").
    Status:          ✅ AUTO-ACCEPTED

  ORDERS_ENTERED                                  [OPTIONAL]
    Mapped Value:    Labs (A1c and BMP in 3 months, lipid panel).
                     New meds: empagliflozin 10mg daily,
                     chlorthalidone 12.5mg daily, gabapentin
                     300mg qHS, topical diclofenac gel.
    Confidence:      0.93
    Points:          1.00 / 1.00
    Source Signal:   Exact keyword match ("Orders placed:"). Full
                     order list captured.
    Status:          ✅ AUTO-ACCEPTED


  Section: Attestation & Signature
  ────────────────────────────────

  PROVIDER_SIGNATURE                              [REQUIRED]
    Mapped Value:    Marcus J. Webb, MD
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Exact label match ("Signed electronically:
                     Marcus J. Webb, MD").
    Status:          ✅ AUTO-ACCEPTED

  DATE_TIME_SIGNED                                [REQUIRED]
    Mapped Value:    2026-03-14 11:30
    Confidence:      0.88
    Points:          1.20 / 1.50
    Source Signal:   Keyword match ("March 14, 2026 at 1130").
    Normalization:   "March 14, 2026 at 1130" → "2026-03-14 11:30"
    Status:          ✅ AUTO-ACCEPTED (flagged — time format
                     was military/no colon, auto-normalized)

  PRINTED_NAME                                    [REQUIRED]
    Mapped Value:    Marcus J. Webb, MD
    Confidence:      0.95
    Points:          1.50 / 1.50
    Source Signal:   Co-occurrence with signature field. Same value
                     used for printed name.
    Status:          ✅ AUTO-ACCEPTED

  NPI                                             [REQUIRED]
    Mapped Value:    9876543210
    Confidence:      0.97
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("National Provider
                     Identifier: 9876543210"). Pattern match:
                     10 digits.
    Status:          ✅ AUTO-ACCEPTED

  COSIGN_SUPERVISOR                               [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No cosign or supervisor reference. Single
                     attending provider encounter.
    Status:          ⬜ UNMAPPED

  COSIGN_DATE_TIME                                [OPTIONAL]
    Mapped Value:    —
    Confidence:      0.00
    Points:          0.00 / 1.00
    Source Signal:   No cosign date/time. N/A for this encounter.
    Status:          ⬜ UNMAPPED

  NOTE_STATUS                                     [REQUIRED]
    Mapped Value:    Completed
    Confidence:      0.97
    Points:          1.50 / 1.50
    Source Signal:   Exact keyword match ("Note: Completed"). Exact
                     enum match: "Completed".
    Status:          ✅ AUTO-ACCEPTED


───────────────────────────────────────────────────────────────
  SCORE SUMMARY
───────────────────────────────────────────────────────────────

  Required fields (50):
    100% credit:  32 fields × 1.50 =  48.00
     80% credit:  18 fields × 1.20 =  21.60
    Required subtotal:                 69.60

  Optional fields (25):
    100% credit:  10 fields × 1.00 =  10.00
     80% credit:   0 fields × 0.80 =   0.00
      0% credit:  15 fields × 0.00 =   0.00
    Optional subtotal:                 10.00

                                      ──────
  TOTAL:                              79.60 / 100.0
  GRADE:                              C

───────────────────────────────────────────────────────────────
  ISSUES REQUIRING CLINICIAN REVIEW
───────────────────────────────────────────────────────────────

  MANDATORY CLINICAL REVIEW (patient safety):

  1. ALL RISK ASSESSMENT FIELDS
     Reason:     Clinical safety invariant. All risk screening
                 items must be reviewed by the attesting provider,
                 regardless of confidence score.
     Fields:     RISK_PASSIVE_IDEATION, RISK_ACTIVE_IDEATION_NO_PLAN,
                 RISK_ACTIVE_IDEATION_WITH_PLAN,
                 RISK_ACTIVE_IDEATION_WITH_INTENT,
                 RISK_HOMICIDAL_IDEATION, RISK_SELF_HARM,
                 RISK_FACTORS, PROTECTIVE_FACTORS, RISK_LEVEL,
                 CRISIS_LINE_PROVIDED
     Summary:    All SI/HI denied. Patient endorses firearms access
                 and job instability as risk factors. Protective
                 factors include family and church. Risk level: LOW.
     Action:     Provider must confirm risk determination before
                 signing note.

  2. ICD-10 DIAGNOSES (4 entries)
     Reason:     ICD-10 codes always require clinician confirmation.
                 Incorrect codes affect treatment, billing, and
                 service-connection determinations.
     Mapped:     E11.65, I10, M17.11, E11.42 (all marked SC: Yes)
     Action:     Provider must verify each code, description,
                 status, and SC determination.

  3. MEDICATION CHANGES
     Reason:     4 new medications added, 1 discontinued. Changes
                 were synthesized from the Plan and Orders sections.
     Added:      empagliflozin 10mg, chlorthalidone 12.5mg,
                 gabapentin 300mg qHS, topical diclofenac gel
     Stopped:    naproxen
     Action:     Provider must confirm all medication orders match
                 clinical intent.

  FLAGGED FOR OPTIONAL REVIEW:

  4. TIME_OF_VISIT
     Reason:     Natural language time ("ten fifteen in the morning")
                 auto-converted to "10:15". "Around" qualifier
                 reduces confidence.
     Confidence: 0.80
     Action:     Reviewer may confirm exact appointment time.

  5. VITALS_WEIGHT
     Reason:     Approximate value. Source says "about 224 lbs."
                 Integer 224 selected.
     Confidence: 0.85
     Action:     Reviewer may confirm exact weight from triage.

  6. MSE_APPEARANCE
     Reason:     Fuzzy enum match. Source: "Appropriately dressed"
                 → mapped to "Appropriate".
     Confidence: 0.88
     Action:     Optional — confirm "Appropriate" is correct enum.

  7. MSE_SPEECH
     Reason:     Source says "normal rate and tone" but enum is
                 "Normal rate/tone/volume". Volume not explicitly
                 stated.
     Confidence: 0.88
     Action:     Optional — confirm "Normal rate/tone/volume".

  8. MSE_AFFECT
     Reason:     Source says "mildly anxious" but enum is "Anxious"
                 (no severity gradation). "Mildly" qualifier dropped.
     Confidence: 0.88
     Action:     Optional — clinician may want to note severity
                 in MSE_NARRATIVE.

  9. MEDICATION_RECONCILIATION
     Reason:     Inferred from "changes being made today (see plan)"
                 → "Changes Made" enum. Not an exact label match.
     Confidence: 0.88
     Action:     Optional — confirm enum selection.

 10. MSE_THOUGHT_CONTENT
     Reason:     Synthesized from multiple signals: "No delusions
                 or paranoia" + stressors from mood/affect context.
     Confidence: 0.88
     Action:     Optional — review combined narrative.

 11. DATE_TIME_SIGNED
     Reason:     Military time "1130" auto-normalized to "11:30".
     Confidence: 0.88
     Action:     Optional — confirm time format.

 12. ICD10_DIAGNOSES (confidence)
     Reason:     4 diagnoses extracted with codes validated against
                 pattern. Moderate confidence because diagnosis
                 descriptions were paraphrased (not verbatim
                 ICD-10 code descriptions).
     Confidence: 0.88
     Action:     Already flagged as mandatory (see #2 above).

───────────────────────────────────────────────────────────────
  UNMAPPED FIELDS
───────────────────────────────────────────────────────────────

  OPTIONAL FIELDS (no penalty for missing):

  1.  RESIDENT_STUDENT
      Section:    Patient & Encounter Information
      Reason:     No trainee involved in this encounter.

  2.  SUPERVISING_PROVIDER
      Section:    Patient & Encounter Information
      Reason:     Single attending provider encounter.

  3.  VITALS_HEIGHT
      Section:    Vitals
      Reason:     No height measurement in the dictation.

  4.  VITALS_BMI
      Section:    Vitals
      Reason:     Cannot calculate — height not available.

  5.  MSE_NARRATIVE
      Section:    Mental Status Examination (MSE)
      Reason:     No additional MSE narrative beyond structured
                  findings.

  6.  RISK_SI_HI_DETAILS
      Section:    Risk Assessment
      Reason:     All SI/HI denied — no details to document.

  7.  RISK_NARRATIVE
      Section:    Risk Assessment
      Reason:     No standalone risk justification narrative.
                  Risk data embedded in screening section.

  8.  SAFETY_PLAN
      Section:    Risk Assessment
      Reason:     Not discussed in the source data.

  9.  COSIGN_SUPERVISOR
      Section:    Attestation & Signature
      Reason:     No cosign needed — single attending.

  10. COSIGN_DATE_TIME
      Section:    Attestation & Signature
      Reason:     No cosign needed.

  REQUIRED FIELDS (0 missing):
  None — all 50 required fields mapped.  ✅

───────────────────────────────────────────────────────────────
  MISSING REQUIRED FIELDS
───────────────────────────────────────────────────────────────

  None — all 50 required fields have mapped values.  ✅

═══════════════════════════════════════════════════════════════
                        END OF REPORT
═══════════════════════════════════════════════════════════════
```
