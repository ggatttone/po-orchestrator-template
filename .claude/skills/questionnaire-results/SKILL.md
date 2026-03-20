# /questionnaire-results

Review and summarize questionnaire responses collected for a specific epic. Produces a pre-workshop briefing with consensus, conflicts, and actionable items.

## Usage

```
/questionnaire-results [epic-id] [--update-epic]
```

## What this skill does

1. Reads questionnaire response data for the epic (from database or provided JSON)
2. Reads the epic context from `epics/{epic-id}/epic-context.md`
3. Delegates to the `workshop-designer` agent (Mode 2: Post-Questionnaire Analysis)
4. Displays the aggregated analysis to the user
5. If `--update-epic` is provided and user confirms: updates `open-questions.md` and `requirements-log.md`

## Input

| Parameter | Required | Description |
|---|---|---|
| `epic-id` | Yes | The epic directory name under `epics/` |
| `--update-epic` | No | After review, push new requirements and OQ answers into epic files. User must confirm. |

## Step 1 — Get response data

<!-- CUSTOMIZE: Configure how to retrieve questionnaire responses.
     Options include:
     - Database query (Supabase, Postgres, etc.)
     - JSON file in the epic directory
     - Manual paste from the user
-->

## Step 2 — Aggregate and analyze

Pass the data to the workshop-designer agent in **Mode 2**. The agent produces:

### Output structure

```markdown
# Questionnaire Results — [Epic Name]

**Epic**: [Jira Key] | [Your Program]
**Responses**: [N] respondents
**Period**: [first response date] — [last response date]

---

## Response Summary

| Respondent | Role | Date | Key contribution |
|---|---|---|---|
| [Name] | [Role] | [Date] | [1-line summary] |

---

## Consensus Items (agreed by 2+ respondents)

| # | Topic | What respondents agree on | Confidence |
|---|---|---|---|
| 1 | [topic] | [consensus statement] | [avg confidence] |

---

## Conflict Items (respondents disagree)

| # | Topic | Position A | Position B | Needs decision |
|---|---|---|---|---|
| 1 | [topic] | [view] | [view] | Yes/No |

---

## New Requirements (extracted from responses)

| # | Title | Description | Source | Confidence |
|---|---|---|---|---|
| 1 | [title] | [description] | [respondent] | [score] |

---

## Open Questions — Answered

| # | Original question | Answer | Answered by |
|---|---|---|---|
| 1 | [question from open-questions.md] | [answer from response] | [respondent] |

---

## Open Questions — New (raised by respondents)

| # | Question | Raised by |
|---|---|---|
| 1 | [question] | [respondent] |

---

## Pain Points (ranked by frequency)

| Rank | Pain point | Mentioned by | Severity | Workaround |
|---|---|---|---|---|
| 1 | [pain point] | [N respondents] | [high/med/low] | [workaround] |

---

## Process Steps (unified SIPOC)

| Step | Name | Who | System | Source |
|---|---|---|---|---|
| 1 | [step] | [role] | [system] | [respondent(s)] |

---

## Metrics

| Metric | Values reported | Range |
|---|---|---|
| Volume | [values from respondents] | [min — max] |
| Processing time | [values] | [range] |
| Error rate | [values] | [range] |

---

## Recommendations for Workshop

1. [Actionable recommendation based on consensus/conflicts]
2. [Topic that needs discussion due to conflicting views]
3. [Gap that no respondent addressed — needs workshop time]

---

*Generated from [N] questionnaire responses. Use this as pre-workshop preparation — not as final requirements.*
```

## Step 3 — Optional epic file updates (if `--update-epic`)

If the user confirms:

1. **`open-questions.md`**: Mark answered questions as "Answered" with the answer text and respondent name
2. **`requirements-log.md`**: Add new requirements extracted from responses (with `[Source: Questionnaire — respondent name]` tag)

Rules:
- Never overwrite existing content — only add or update status
- New requirements get the next available REQ ID
- Answered OQs keep their original question text, add answer + date + source

## Rules

- Never invent data. Only aggregate what respondents explicitly stated.
- If only 1 response exists, skip consensus/conflict analysis — just present the single response.
- Translate non-English responses to English in the output, preserving the original in parentheses.
- Mark low-confidence items (< 0.6) with a warning.
- If no responses exist, inform the user and suggest sharing the survey URL.

## Delegate to

Use the `workshop-designer` agent in **Mode 2: Post-Questionnaire Analysis**.
