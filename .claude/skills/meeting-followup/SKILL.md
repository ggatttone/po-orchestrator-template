# /meeting-followup

Process meeting notes from scrum ceremonies and other regular meetings, extract structured content, and distribute to the relevant epic files.

## Usage
`/meeting-followup [epic-id]`

## What this skill does

1. Prompts the user for meeting notes (file path or pasted text)
2. Identifies the meeting type: sprint-planning, sprint-review, backlog-refinement, standup, stakeholder-alignment, other
3. Reads the epic context from `epics/{epic-id}/epic-context.md`
4. Reads `knowledge/way-of-working.md` for meeting/ceremony context
5. Reads `knowledge/glossary.md` for terminology
6. Delegates to `document-analyst` agent for structured extraction
7. Distributes extracted content to epic files:
   - **Action items** -> append to `epics/{epic-id}/action-items.md`
   - **Decisions** -> append to `epics/{epic-id}/decisions-log.md`
   - **Open questions** -> append to `epics/{epic-id}/open-questions.md`
   - **Scope changes** -> flag for review, suggest updating `epic-context.md`
8. Produces a meeting summary document
9. Optionally saves the summary to `reference-docs/meetings/Meeting_{Type}_{Epic}_{Date}.md`

## Input

The user provides:
- **epic-id** (required): The epic directory name under `epics/`
- Meeting notes via one of:
  - File path (local or OneDrive)
  - Pasted text in the next message

## Workflow detail

### Step 1: Receive notes and identify meeting type

Ask the user (if not obvious from the notes):
- What type of meeting? (sprint-planning, sprint-review, backlog-refinement, standup, stakeholder-alignment, other)
- What was the date of the meeting?

### Step 2: Analyze

- Delegate to `document-analyst` agent
- Focus extraction on: action items, decisions, open questions, scope changes, key discussion points

### Step 3: Distribute to epic files

For each category, show the user what will be added before writing:

**Action items** — append format:
```
- [ ] [Action text] — Owner: [name] — Due: [date if stated] — Source: [Meeting type] [date]
```

**Decisions** — append format:
```
- [D-NNN]: [Decision text] — Made by: [person/group] — Date: [meeting date] — Source: [Meeting type]
```

**Open questions** — append format:
```
- [Q-NNN]: [Question text] — Raised by: [person] — Date: [meeting date] — Source: [Meeting type]
```

**Scope changes** — do NOT write automatically. Present to user with:
```
Scope change detected: [description]. Update epic-context.md? [REVIEW]
```

### Step 4: Produce meeting summary

Generate the structured summary (see output format below).

### Step 5: Suggest follow-up

Based on meeting type:
- Sprint review -> "Run `/status-update` to draft a status update for this sprint?"
- Sprint planning -> "Run `/sprint-prep` to check story readiness?"

## Output format

```markdown
# Meeting Summary: [Meeting Type] — [Epic Name]

**Date**: [meeting date]
**Type**: [Sprint Planning / Sprint Review / Backlog Refinement / Standup / Stakeholder Alignment / Other]
**Attendees**: [if known from notes]
**Epic**: [epic-id] ([Jira key])

## Key Discussion Points

- [Point 1]
- [Point 2]

## Decisions Made ([count])

- [D-NNN]: [Decision text] — [Made by] — [Date]

## Action Items ([count])

- [ ] [Action] — Owner: [name] — Due: [date] — Source: [meeting type] [date]

## Open Questions ([count])

- [Q-NNN]: [Question] — Raised by: [person] — Date: [date]

## Scope Changes Flagged

- [Change description] — [REVIEW: Update epic-context.md?]

## Integration Summary

- [N] action items added to action-items.md
- [N] decisions added to decisions-log.md
- [N] questions added to open-questions.md
- [N] scope changes flagged for review
```

## Differences from /workshop-followup

| Aspect | /workshop-followup | /meeting-followup |
|---|---|---|
| Meeting types | Workshops only | Scrum ceremonies + regular meetings |
| Saves to | `reference-docs/workshops/` | `reference-docs/meetings/` |
| Scope changes | Not tracked | Flagged with `[REVIEW]` |
| Draft requirements | Extracted and offered | Not extracted (meetings produce actions, not requirements) |
| Follow-up suggestions | No | Yes (suggests related skills based on meeting type) |

## Rules

- Never modify existing items in epic files — only append new ones.
- Mark new action items as unchecked (`- [ ]`).
- Auto-increment decision/question numbers based on existing entries in the epic files.
- If a meeting spans multiple epics, ask the user which items go where.
- Never invent content not in the meeting notes.
- Scope changes are NEVER written automatically — always present for user review.

## Delegate to

Use the `document-analyst` agent for extraction, then handle distribution directly.
