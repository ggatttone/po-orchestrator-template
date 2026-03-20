# /elicitation-pack

Produce a Business Elicitation Pack to send to stakeholders before a requirements workshop. The pack contains structured questions that business users complete in advance, so they arrive prepared with the right process knowledge.

## Usage

```
/elicitation-pack [epic-id] [--workshop-date YYYY-MM-DD] [--participants "Name (Role), Name (Role)"]
```

## What this skill does

1. Reads the epic context from `epics/{epic-id}/epic-context.md` (objective, scope, stakeholders, related epics)
2. Reads `epics/{epic-id}/open-questions.md` for existing open items to include in Slide D
3. Reads `epics/{epic-id}/decisions-log.md` for pending decisions to include in Slide D
4. Reads `epics/{epic-id}/requirements-log.md` for known process fragments to use as candidate steps in Slide C
5. Reads `knowledge/glossary.md` for correct terminology
6. Reads `knowledge/elicitation-question-bank.md` for the Minimum Question Battery (filtered by epic domain)
7. Delegates to the `workshop-designer` agent to produce the full pack
8. Displays the complete pack to the user for review
9. Asks the user to confirm before saving to `epics/{epic-id}/elicitation-pack-{date}.md`

## Input

| Parameter | Required | Description |
|---|---|---|
| `epic-id` | Yes | The epic directory name under `epics/` |
| `--workshop-date YYYY-MM-DD` | No | Date of the workshop. If omitted, the pack shows "TBD" in the date fields. |
| `--participants "..."` | No | Comma-separated list of "Name (Role)" entries. If omitted, the agent derives participants from the epic-context.md Key stakeholders table. |

## Output format

```markdown
# Business Elicitation Pack — [Epic Name]

**Epic**: [Jira Key] | [Your Program]
**Workshop date**: [date or TBD]
**Purpose**: [Epic Objective, 1 sentence]
**Prepared by**: Product Owner
**Please complete before**: [2 business days before workshop, or TBD]

---

## Before you start
[instructions for business users]

---

## Slide A — Context and Objective
[What the workshop is about | Scope table | Expected outputs | Participants table]

## Slide B — Process Definition Questions
[5 questions for business to answer: Definition | Trigger | End condition | Customer | Inputs/Outputs]

## Slide C — As-Is Process (SIPOC view)
[5-7 step table | Top 3 variants | Handoffs and dependencies]

## Slide D — Value, Metrics, Pain Points, and Open Decisions
[Why it exists | Metrics table | Pain points table | Decisions expected | Open questions]

## Minimum Question Battery
[20 core questions grouped by category + domain-specific additions]

---

*[Your Company] | [Your Program] | Confidential*
```

## Pre-population rules

The agent pre-fills known content and marks gaps clearly:

| Pack section | Source | Pre-fill logic |
|---|---|---|
| Slide A — Objective | `epic-context.md` Objective | Direct copy — cited as *(from epic context)* |
| Slide A — Scope | `epic-context.md` In/Out scope | Direct copy as table rows — cited |
| Slide A — Participants | `epic-context.md` Key stakeholders | Map to Sponsor / SME / Ops / IT / Facilitator |
| Slide C — Step names | `requirements-log.md` req titles | Extract action verb + object as candidate steps — marked *(candidate — confirm or replace)* |
| Slide C — Systems | `knowledge/systems-landscape.md` | Relevant systems for epic type — cited |
| Slide D — Decisions | `decisions-log.md` Pending/Open | Direct copy as questions, max 3 |
| Slide D — Open questions | `open-questions.md` Open items | Direct copy, max 5 |
| Question Battery | `elicitation-question-bank.md` | Filtered by epic domain tag |

All fields with no source data use `[ANSWER NEEDED]` — never left blank.

## Rules

- Never invent process steps, roles, metrics, or pain points not present in epic files.
- Mark every pre-filled field with its source in italics.
- Mark every unknown field with `[ANSWER NEEDED]`.
- Slide C must have 5-7 step rows. If fewer than 5 candidate steps exist, add `[ANSWER NEEDED]` rows.
- The Minimum Question Battery always contains all 20 core questions. Domain-specific additions are appended after the core battery.
- Slide B questions are always `[ANSWER NEEDED]` for the business's answers — the agent writes only the question text.
- Never modify existing epic files. The elicitation pack is a new output file only.
- Output language: English. Non-English terms from the epic context are preserved in parentheses.

## Delegate to

Use the `workshop-designer` agent for pack content generation.
