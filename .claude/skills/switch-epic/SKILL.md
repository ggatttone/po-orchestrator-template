# /switch-epic

Switch active context to a different epic.

## Usage
`/switch-epic [epic-id]`

## What this skill does

1. Reads the Active Epics Registry from `CLAUDE.md` to find the epic
2. Reads `epics/{epic-id}/epic-context.md` — scope, objectives, current sprint focus
3. Reads `epics/{epic-id}/open-questions.md` — unresolved items
4. Reads `epics/{epic-id}/decisions-log.md` — recent decisions
5. Reads `epics/{epic-id}/action-items.md` — open action items
6. Calculates the epic's priority score using the pm-support Mode 5 algorithm (reads calendar events and cross-epic data)
7. Checks for upcoming deadlines within the next 7 days (from calendar or epic-context.md)
8. Checks for cross-epic dependency warnings (from epic-context.md "Related epics" field)
9. Prints a brief context summary:
   - Epic name, status, and objective
   - **Priority score** (0-100 with dimension breakdown)
   - Current sprint focus
   - **Upcoming deadlines** (next 7 days)
   - **Dependency warnings** (if epic blocks or is blocked by others)
   - Number of open questions
   - Number of open action items
   - Recent decisions (last 3)
10. Confirms the context switch

## Output format

```
Context loaded: [Epic Name] ([Epic Key])
Status: [Status]
Priority: [N]/100 (Urgency: [N] | Deps: [N] | Stakeholder: [N] | Sprint: [N] | Questions: [N])
Sprint focus: [Current focus]
Deadlines: [Deadline type] in [N] days ([Date]) — or "No deadlines within 7 days"
Dependencies: [Blocks: Epic X | Blocked by: Epic Y] — or "No cross-epic dependencies"
Open questions: [N]
Open action items: [N] ([M] overdue)
Recent decisions:
- [D-latest]: [Decision summary]
- [D-previous]: [Decision summary]

Ready to work on [Epic Name].
```

## If epic not found

If the specified epic directory does not exist under `epics/`:
1. List available epic directories
2. Ask the user to confirm or create a new epic from the template
