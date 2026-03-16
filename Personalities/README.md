# Personalities

A collection of 10 fictional human personas for AI agent role-play, simulation, testing, and narrative design.

---

## Index

| # | Folder | Persona | Occupation | Age | Location |
|---|--------|---------|-----------|-----|----------|
| 1 | [Chaplain-Hospital](Chaplain-Hospital/) | Margaret (Maggie) Chen-Halvorsen | Board Certified Hospital Chaplain | 56 | Philadelphia, PA |
| 2 | [Doctor-EmergencyMedicine](Doctor-EmergencyMedicine/) | Priya Ramanathan | Emergency Medicine Physician | 38 | Houston, TX |
| 3 | [Electrician-Commercial](Electrician-Commercial/) | Ray Kowalski | Commercial Electrician (IBEW Journeyman) | 44 | Denver, CO |
| 4 | [Firefighter-Captain](Firefighter-Captain/) | Thomas (Tommy) Redhawk | Fire Captain | 46 | Phoenix, AZ |
| 5 | [Lawyer-CriminalDefense](Lawyer-CriminalDefense/) | Marcus Delacroix | Criminal Defense Attorney | 42 | Chicago, IL |
| 6 | [Nurse-ICU](Nurse-ICU/) | James Okafor-Whitfield | ICU Registered Nurse (Charge Nurse) | 34 | Minneapolis, MN |
| 7 | [Plumber-Residential](Plumber-Residential/) | Sandra Thibodeau | Master Plumber / Small Business Owner | 51 | Suburban Boston, MA |
| 8 | [PoliceOfficer-Patrol](PoliceOfficer-Patrol/) | Elena Vasquez-Torres | Police Officer (Patrol / CIT) | 35 | Portland Metro, OR |
| 9 | [Politician-CityCouncil](Politician-CityCouncil/) | David Nkemelu | City Council Member / Nonprofit Director | 48 | Suburban GA |
| 10 | [Teacher-HighSchool](Teacher-HighSchool/) | Aisha Patel-Robinson | High School English Teacher (AP / American Lit) | 40 | Suburban NC |

---

## Folder Structure

Each persona subfolder contains three files:

```
<Persona-Folder>/
├── persona.json      # Structured character data (JSON schema v1.0)
├── DESCRIPTION.md    # Human-readable personality summary
└── INSTRUCTION.md    # AI agent usage guide for embodying the persona
```

### File Descriptions

| File | Format | Purpose |
|------|--------|---------|
| **persona.json** | JSON | Complete persona specification — identity, location, education, occupation, socioeconomic context, Big Five psychometrics, decision and communication styles, values/motivations/fears, health context, behavioral profile, backstory, dual scenario hooks (as professional + as client), dialogue samples, and quality checks |
| **DESCRIPTION.md** | Markdown | Quick-reference summary of the persona's key traits, values, professional and personal context, and distinguishing characteristics |
| **INSTRUCTION.md** | Markdown | Step-by-step instructions for an AI agent: role adoption rules, behavioral constraints (trigger phrases, taboo topics, moral red lines), scenario usage guidance, dialogue calibration, backstory integration, and quality guardrails |

### Root Files

| File | Purpose |
|------|---------|
| [DESCRIPTION.md](DESCRIPTION.md) | Detailed overview of the collection — what it contains, why it's useful, use cases, problems solved, and quality standards |
| [README.md](README.md) | This file — index and structural reference |
| [personality-generator-prompt.md](personality-generator-prompt.md) | The prompt template and JSON schema used to generate persona files |

---

## Occupations Covered

The personas span six professional sectors:

| Sector | Personas |
|--------|----------|
| **Healthcare** | Doctor (Emergency Medicine), Nurse (ICU), Chaplain (Hospital) |
| **Legal** | Lawyer (Criminal Defense) |
| **Public Safety** | Police Officer (Patrol), Firefighter (Captain) |
| **Trades** | Electrician (Commercial), Plumber (Residential) |
| **Education** | Teacher (High School English) |
| **Government / Nonprofit** | Politician (City Council) |

---

## Diversity Profile

The collection was designed for high diversity across multiple dimensions:

- **Age range:** 34–56
- **Gender:** 5 female, 5 male
- **Race/ethnicity:** Black, White, Asian, Multiracial, American Indian, Hispanic/Latina
- **Geography:** 10 different U.S. states spanning urban and suburban settings
- **Income:** $52K–$340K across trades, public service, professional, and medical occupations
- **Education:** High school + trade school through M.D. and J.D.
- **Family status:** Single, married, divorced, widowed, partnered — with and without children
- **Life stage:** Early career through late career / pre-retirement

All diversity is descriptive — personality traits, competence, and behavior are never derived from demographic identity.

---

## JSON Schema Highlights

Every `persona.json` follows **schema version 1.0** with these key sections:

| Section | What It Contains |
|---------|-----------------|
| `identity` | Name, age, gender, pronouns, race/ethnicity, languages, religion, family status, disability/access needs |
| `location_context` | Country, region, urbanicity, housing, community ties |
| `education_and_training` | Degrees, certifications, licenses, years of experience, continuing education style |
| `occupation_profile` | Title, specialization, setting, responsibilities, tools, regulatory context, constraints |
| `socioeconomic` | Income bracket, financial pressures, insurance/benefits |
| `psychometrics` | Big Five OCEAN scores (0–100), decision style, communication style (tone, verbosity, formality, triggers, taboos) |
| `values_and_motivations` | Core values, motivators, fears, moral red lines, sources of pride |
| `health_and_wellbeing` | General health, stressors, coping strategies, sleep/energy patterns |
| `behavioral_profile` | Habits, hobbies, media diet, social circle, trust baseline, cognitive biases to watch |
| `backstory` | One-paragraph summary (70–120 words), formative events, current challenges, near-term goals, long-term aspirations |
| `scenario_hooks` | Dual perspectives — as a professional (cases, dilemmas, ethics) and as a client/patient (problems, constraints, frustrations) |
| `dialogue_samples` | Four calibration samples: greeting, under stress, when satisfied, when refusing |
| `quality_checks` | Internal consistency pass, non-stereotyping pass, and explanatory notes |

---

## Quick Start

### For AI Agent Developers

1. Pick a persona folder based on the occupation you need
2. Load `persona.json` into your agent's context or system prompt
3. Follow the rules in `INSTRUCTION.md` for consistent character embodiment
4. Use `DESCRIPTION.md` for a quick human-readable reference

### For Simulation / Training Designers

1. Review the [DESCRIPTION.md](DESCRIPTION.md) for use cases and the problem these personas solve
2. Use `scenario_hooks.as_a_professional` to set up scenarios where the persona is the expert
3. Use `scenario_hooks.as_a_client_or_patient` to set up scenarios where the persona is seeking help
4. Use `dialogue_samples` as tone anchors for voice calibration

### For Testing and QA

1. Use `trigger_phrases` to stress-test emotional handling
2. Use `moral_red_lines` to verify boundary enforcement
3. Use `biases_to_watch` to audit for cognitive bias reproduction
4. Use `quality_checks` to validate consistency and non-stereotyping compliance
