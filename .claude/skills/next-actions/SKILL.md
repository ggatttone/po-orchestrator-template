# /next-actions

Show the top 5 next actions across all epics, sorted by priority score and filtered by time context.

## Usage
`/next-actions [now|today|this-week]`

## What this skill does

1. Reads `epic-context.md` from every epic directory under `epics/` (skips `_template` and `_example`)
2. Reads `action-items.md` from every epic to find open action items
3. Reads `open-questions.md` from every epic for blocking questions requiring action
4. Reads calendar events (ICS URL or manual fallback) for time-sensitive items
5. Calculates priority scores using the Mode 5 algorithm
6. Filters and ranks the top 5 actions based on context:
   - `now`: Immediate actions (meetings starting soon, overdue items, blockers)
   - `today`: Actions doable today (based on calendar gaps and priorities)
   - `this-week`: Actions for the current week (broader view)
7. Delegates to `pm-support` agent (priority analysis mode)
8. Console output only (not saved to file)

## Input

The user provides:
- **context** (optional): One of `now`, `today`, `this-week`. Default: `today`

## Output format

```markdown
# Next Actions — [Context]

**Date**: [today] [time if "now"]

| # | Action | Epic | Priority | Due | Source |
|---|---|---|---|---|---|
| 1 | [Action description] | [epic-id] | [Score]/100 | [Due date or "—"] | [action-items / open-questions / calendar] |
| 2 | [Action description] | [epic-id] | [Score]/100 | [Due date or "—"] | [source] |
| 3 | [Action description] | [epic-id] | [Score]/100 | [Due date or "—"] | [source] |
| 4 | [Action description] | [epic-id] | [Score]/100 | [Due date or "—"] | [source] |
| 5 | [Action description] | [epic-id] | [Score]/100 | [Due date or "—"] | [source] |

**Tip**: [Contextual suggestion, e.g., "Run `/switch-epic [top-epic]` to load context for the highest priority epic."]
```

## Action sources

Actions are gathered from three sources:
1. **action-items.md**: Open items (`- [ ]`) with owner and due dates
2. **open-questions.md**: Blocking questions that require the PO's input
3. **Calendar events**: Upcoming meetings that require preparation

## Filtering logic

- `now`: Only items that are overdue, due today, or related to the next calendar event within 2 hours
- `today`: Items that are overdue, due today, or related to any calendar event today
- `this-week`: Items that are overdue, due this week, or related to any calendar event this week

## Rules

- Maximum 5 actions displayed. If more are available, note: "[N] additional actions not shown."
- Actions inherit the priority score of their parent epic.
- Console output only — do not save to a file.
- Skip `_template` and `_example` directories when scanning epics.
- If no actions are found, report: "No pending actions found. All clear!"
- Omit the table header row if no actions exist.

## Delegate to

Use the `pm-support` agent (priority analysis mode).
