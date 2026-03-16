# Community Governance Policies — Example 1

A comprehensive policy framework for governing AI agent communities deployed on Azure AI Foundry.

---

## What This Is

This folder contains **governance and guardrail policies** that define what AI agents in a community can and cannot do. These policies work alongside the persona-level instructions (INSTRUCTION.md) and knowledge files (persona.json) to create a layered governance model:

```
┌─────────────────────────────────────────┐
│  1. Global Policy     (highest priority) │ ← Applies to ALL agents
├─────────────────────────────────────────┤
│  2. Safety Policy                        │ ← Crisis protocols, prohibited content
├─────────────────────────────────────────┤
│  3. Domain Policies                      │ ← Medical, Legal, Financial, etc.
├─────────────────────────────────────────┤
│  4. Tone Policy                          │ ← Communication standards
├─────────────────────────────────────────┤
│  5. Persona Instructions (INSTRUCTION.md)│ ← Per-agent behavioral rules
├─────────────────────────────────────────┤
│  6. Persona Knowledge (persona.json)     │ ← Per-agent character data
└─────────────────────────────────────────┘
```

Higher-priority policies override lower ones when conflicts arise.

---

## File Structure

```
Policies-Example-1/
├── manifest.json                    # Policy registry — lists all policies, priorities, and scope
├── README.md                        # This file
└── policies/
    ├── global.md                    # Foundational rules for all agents
    ├── safety.md                    # Safety guardrails and crisis protocols
    ├── tone.md                      # Communication standards and tone baseline
    └── domains/
        ├── medical.md               # Healthcare domain guardrails
        ├── legal.md                 # Legal domain guardrails
        ├── financial.md             # Financial/investment/tax domain guardrails
        ├── public-safety.md         # Law enforcement and fire service guardrails
        └── trades.md                # Skilled trades (electrical, plumbing) guardrails
```

---

## Policy Summary

| Policy | ID | Priority | Scope | Covers |
|--------|----|----------|-------|--------|
| **Global** | POLICY-GLOBAL-001 | 1 (highest) | All agents | Identity, accuracy, privacy, consent, escalation, unauthorized practice, auditability |
| **Safety** | POLICY-SAFETY-001 | 2 | All agents | Prohibited content, crisis protocols (suicide, harm to others, child safety, DV), medical/financial/legal safety, jailbreak resistance |
| **Medical** | POLICY-DOMAIN-MEDICAL-001 | 3 | Doctor, Nurse, Chaplain | Education vs. diagnosis, emergency recognition, evidence standards, patient autonomy, HIPAA awareness |
| **Legal** | POLICY-DOMAIN-LEGAL-001 | 3 | Lawyer, Financial Advisors, Politician | Education vs. practice, criminal/tax/securities/estate law, privilege boundaries, jurisdictional awareness |
| **Financial** | POLICY-DOMAIN-FINANCIAL-001 | 3 | All 3 Financial Advisors | Education vs. advice, suitability, investment/tax/retirement rules, behavioral finance |
| **Public Safety** | POLICY-DOMAIN-PUBLICSAFETY-001 | 3 | Police Officer, Firefighter | Emergency situations, law enforcement topics, fire safety, use of force, first responder mental health |
| **Trades** | POLICY-DOMAIN-TRADES-001 | 3 | Electrician, Plumber | Safety requirements, code compliance, DIY vs. professional boundaries, diagnostic guidance |
| **Tone** | POLICY-TONE-001 | 4 | All agents | Respect, inclusivity, professionalism, clarity, emotional responsiveness, persona overlay |

---

## How to Use These Policies

### For Orchestrator Agent Instructions
Reference the policy hierarchy in the orchestrator's system prompt so it understands governance structure:
> "All connected agents operate under a governance framework. Global policy and safety policy take precedence over all other instructions."

### For Individual Agent Instructions
Include relevant domain policies as additional context in each agent's INSTRUCTION.md or as supplemental knowledge files:
> "In addition to your persona instructions, you are bound by the Global Policy, Safety Policy, and [Domain] Policy. When conflicts arise, policy takes precedence over persona."

### For Knowledge (Vector Store)
Upload policies as knowledge files alongside persona.json so agents can reference specific rules via file search when needed.

### For Compliance Review
Use manifest.json to programmatically identify which policies apply to which agents, enabling automated compliance checking and audit reporting.

---

## Extending This Framework

To add a new domain policy:

1. Create a new `.md` file in `policies/domains/` following the existing format
2. Add the entry to `manifest.json` with the correct priority, scope, and description
3. Update affected agents' instructions to reference the new policy
4. Optionally upload the policy as a knowledge file to affected agents' vector stores

To adapt for a different community (e.g., military, healthcare-only, legal services):
- Keep `global.md`, `safety.md`, and `tone.md` as the universal baseline
- Replace or add domain policies as needed for the community's specialization
- Update `manifest.json` to reflect the new policy set
