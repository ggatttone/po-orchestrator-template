# /workshop-followup

Process workshop or meeting notes and distribute extracted content to the relevant epic files.

## Usage
`/workshop-followup [epic-id]`

## What this skill does

1. Prompts the user for workshop notes (file path or pasted text)
2. Reads the epic context from `epics/{epic-id}/epic-context.md`
3. Reads `knowledge/requirement-standards.md` and `knowledge/glossary.md`
4. Delegates to `document-analyst` for structured extraction
5. Distributes extracted content to the epic files:
   - **Action items** -> append to `epics/{epic-id}/action-items.md`
   - **Decisions** -> append to `epics/{epic-id}/decisions-log.md`
   - **Open questions** -> append to `epics/{epic-id}/open-questions.md`
   - **Draft requirements** -> present to user for review, then optionally delegate to `requirements-analyst` for formalization
6. Saves original workshop notes to `reference-docs/workshops/` with naming convention `Workshop_{Topic}_{YYYY-MM-DD}.md`

## Input

The user provides:
- **epic-id** (required): The epic directory name under `epics/`
- Workshop notes via one of:
  - File path (local or OneDrive)
  - Pasted text in the next message

## Workflow detail

### Step 1: Receive and save original notes
- If pasted: save to `reference-docs/workshops/Workshop_{Topic}_{Date}.md`
- If file: note the path, do not move/copy

### Step 2: Analyze
- Delegate to `document-analyst` agent
- Focus extraction on: action items, decisions, open questions, **scenarios from discussions**, draft requirements

### Step 3: Distribute to epic files
For each category, show the user what will be added before writing:

**Action items** — append format:
```
- [ ] [Action text] — Owner: [name] — Due: [date if stated] — Source: Workshop [date]
```

**Decisions** — append format:
```
- [D-NNN]: [Decision text] — Made by: [person/group] — Date: [workshop date] — Source: Workshop [topic]
```

**Open questions** — append format:
```
- [Q-NNN]: [Question text] — Raised by: [person] — Date: [workshop date] — Source: Workshop [topic]
```

**Scenarios** — link to the relevant requirement in requirements-log.md:
- Each scenario must have an origin tag (`Explicit (Transcript)`, `Explicit (Workshop notes)`, etc.)
- Scenarios are attached to requirements, not stored separately
- If a scenario doesn't map to any requirement, flag it for review
- Quote or paraphrase original discussion; include original language in parentheses when helpful

**Draft requirements** — present to user, ask if they want to formalize via `requirements-analyst`

### Step 4: Confirm
- Show summary of what was added to each file
- Show count: X action items, Y decisions, Z questions, W draft requirements

## Rules

- Always save original notes before processing.
- Never modify existing items in epic files — only append new ones.
- Mark new action items as unchecked (`- [ ]`).
- Auto-increment decision/question numbers based on existing entries.
- If a workshop spans multiple epics, ask the user which items go where.
- Never invent content not in the workshop notes.

## Delegate to

Use the `document-analyst` agent for the extraction, then handle distribution directly.
