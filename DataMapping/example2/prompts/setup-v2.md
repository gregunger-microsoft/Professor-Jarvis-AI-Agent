# SETUP PROMPT — CPRS Primary Care Progress Note (v2)

## Role & Objective

You are an expert **Clinical Information Architect, Healthcare Documentation Specialist, and Document Systems Designer** with deep knowledge of VA/VHA clinical documentation standards, CPRS (Computerized Patient Record System), and medical data modeling.

Your task is to generate a **complete document-hydration solution** for a VA CPRS Primary Care Progress Note — consisting of an authoritative JSON schema, a data-mapping specification, synthetic test source data, and a README — all created directly by AI inference and written as files.

---

## Context

This example is part of a **Document Hydration Solution** that demonstrates automated extraction of structured field values from unstructured source data and hydration into standardized document templates.

### Reference Materials

| Artifact | Location | Purpose |
|---|---|---|
| Pattern guide (Example 1) | `../example1/` | Prior working example — Enterprise Service Request. Follow the same structural patterns, file conventions, and quality standards. |
| Master creation prompt | `../../create-example-prompt.txt` | Global rules and deliverable definitions for the document hydration solution. |
| CPRS template (source form) | `../forms/CPRS_PCP_ProgressNote_Template.docx` | The VA CPRS Primary Care Progress Note template. This is the authoritative visual form. All schema fields must have a 1:1 correspondence with fields in this template. |
| Data mapping overview | `../../data-mapping-creation-prompt.md` | Conceptual overview of the mapping approach. |

### Target Directory

All artifacts must be created under:

```
C:\tempGregProj\ProfessorJarvisAIAgent\DataMapping\example2\
```

Using this folder structure:

```
example2/
├── artifacts/
│   ├── schema.json
│   └── DataMappingSpecification.md
├── forms/
│   └── CPRS_PCP_ProgressNote_Template.docx   (already exists — DO NOT modify)
├── source-data/
│   └── sample-input-1.txt
├── prompts/
│   ├── setup.txt          (original — DO NOT modify)
│   ├── setup-v2.md        (this file)
│   ├── execute.txt        (original — DO NOT modify)
│   └── execute-v2.md      (formalized execution prompt)
├── Output/                (created by execute prompt)
└── README.md
```

---

## Global Rules (Non-Negotiable)

1. **No application code, no scripts, no programming languages.**
   This solution relies entirely on AI inference and document generation. You are producing structured data files and documentation — not software.

2. **Schema parity is mandatory.**
   - Every field in the CPRS template **must exist** in the JSON schema.
   - Every field in the JSON schema **must exist** in the CPRS template.
   - Field keys, types, constraints, and section assignments must match exactly.

3. **Deterministic structure.**
   - Minimum **8 sections** (matching the CPRS template sections).
   - Minimum **60 total fields** (the CPRS template has 75).
   - Fields must be clearly identifiable and uniquely named using `SCREAMING_SNAKE_CASE`.

4. **Clinical safety awareness.**
   - Risk assessment fields and ICD-10 diagnoses **always require clinician review** regardless of mapping confidence.
   - The system **never auto-finalizes** a clinical note.
   - All mapped values must be reviewed and attested by a licensed provider.

5. **Human-in-the-loop aware.**
   - The solution must explicitly account for partial mapping, clinical ambiguity, and mandatory review.
   - Separate mandatory review (clinical safety) from optional review (data quality).

6. **Professional quality.**
   - All files must be production-quality, complete, and unambiguous.
   - Do not summarize. Do not provide snippets. Do not explain reasoning. **Create the files.**

---

## Deliverables

### 1. `artifacts/schema.json` — Authoritative Field Definition

Generate a JSON schema that mirrors the CPRS template **exactly**.

For every field, include:

```json
{
  "key": "FIELD_KEY",
  "section": "Section Name",
  "label": "Human Readable Label",
  "description": "What this field represents in clinical context",
  "type": "string | integer | number | boolean | date | enum | array",
  "required": true | false,
  "minLength": null | number,
  "maxLength": null | number,
  "allowedPattern": "regex (if applicable)",
  "allowedCharactersDescription": "plain-English explanation",
  "enumValues": [],
  "example": "example value"
}
```

Schema must also include:
- Schema version
- Total field count
- Creation timestamp
- Section list
- Validation notes (parity confirmation)

**Clinical-specific requirements:**
- ICD-10 codes must validate against pattern `^[A-Z][0-9]{2}(\.[0-9A-Z]{1,4})?$`
- NPI must be exactly 10 digits
- SSN Last 4 must be exactly 4 digits
- Vital signs must have appropriate numeric constraints
- Enum fields must list all valid options matching the template checkboxes

---

### 2. `artifacts/DataMappingSpecification.md` — Mapping & Scoring Guide

Create a detailed specification following the same structure as Example 1's `DataMappingSpecification.md`, adapted for clinical data:

**A. Mapping Strategy**
- How clinical source data (dictations, transcripts, SOAP notes, EHR copy-forwards) is interpreted
- Medical abbreviation recognition (without expansion in output)
- SOAP section detection for contextual inference
- Normalization rules for: vitals, dates, times, ICD-10 codes, NPI, names, enums, pain scale, boolean-like clinical phrases

**B. Mapping Heuristics**
- Clinical keyword and phrase detection banks (for all fields)
- SOAP section → schema section inference table
- Confidence scoring per field (0.0–1.0)
- Conflict resolution with clinical recency and source authority priorities

**C. Failure Handling**
- Field cannot be mapped (with required vs. optional distinction)
- Value violates schema constraints (with auto-correction attempts)
- Source data is ambiguous (with clinical tiebreaker escalation)
- Required fields missing (note cannot be signed)
- Clinical safety escalation (risk present, acute diagnoses)

**D. Mapping Report & Grading**
- 100-point scoring model with required fields weighted more heavily
- Per-field scoring based on confidence tiers
- Grade bands (A–F) with clinical interpretation
- Sample mapping report structure (full field-by-field detail)

**E. Worked Example**
- Sample unstructured physician dictation
- Resulting mapped JSON (all fields)
- Example mapping report summary

---

### 3. `source-data/sample-input-1.txt` — Synthetic Test Source Data

Create a realistic physician dictation that:
- Enables **~80% of fields** to be mapped (high/moderate confidence)
- Deliberately **omits ~20% of fields** (all optional) to test unmapped handling
- Uses **informal clinical language** with abbreviations (HTN, DM, BID, PRN, NAD, CTA, etc.)
- Includes **multiple ICD-10 diagnoses** (3–5) to test array extraction
- Contains **complete risk screening** with risk and protective factors
- Requires **normalization** (natural language dates, narrative times, approximate values)
- Would produce a **Grade B or C** mapping score (75–89 points)

**Clinical realism requirements:**
- Patient demographics (name, DOB, VA ID, SSN last 4)
- Complete vitals from triage
- SOAP-structured clinical narrative
- Mental status exam findings
- Suicide risk screening (all items)
- Physical exam by system
- Lab/imaging results with dates
- 3+ ICD-10 diagnoses with service-connection status
- Multi-problem treatment plan
- Provider signature with NPI
- Note status

---

### 4. `README.md` — Solution Overview & Usage

Document:
- Problem statement (VA clinical documentation burden)
- Use case (semi-automated clinical note completion)
- Solution architecture (with ASCII diagram)
- File inventory with purposes
- Schema overview (field counts by section, key field types)
- Source data design rationale (80/20 split, what's omitted and why)
- Expected mapping results (score, grade, safety flags)
- Clinical safety considerations (5 invariants)
- End-to-end instructions
- Extension guide (other note types, adding fields)
- Comparison with Example 1
- Version history

---

## Validation Checklist

The solution is complete only when:

- [ ] `schema.json` field count matches the CPRS template exactly
- [ ] Every schema field key corresponds to a template field
- [ ] `DataMappingSpecification.md` covers all 5 sections (A–E)
- [ ] Scoring model math sums to exactly 100 points
- [ ] Source data enables ~80% mapping with ~20% unmapped
- [ ] All unmapped fields are optional (no missing required fields)
- [ ] Risk assessment and ICD-10 fields are marked as mandatory clinician review
- [ ] README documents the full end-to-end workflow
- [ ] All files are complete, production-quality, and internally consistent
