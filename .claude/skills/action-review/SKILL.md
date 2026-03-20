# /action-review

Review open action items across one or all epics and produce a prioritized status report.

## Usage
`/action-review [epic-id|all] [--owner=name]`

## What this skill does

1. Reads `action-items.md` from the specified epic(s)
   - If `all`: reads from every epic directory under `epics/`
   - If a specific epic-id: reads only that epic's file
2. Filters to unchecked items (`- [ ]`)
3. Optionally filters by owner (if `--owner=name` is provided)
4. Groups and sorts by urgency:
   - **Overdue**: Due date has passed
   - **Due this week**: Due within the next 7 days
   - **Upcoming**: Due date in the future
   - **No due date**: Items without a due date
5. Outputs a formatted report

## Input

The user provides:
- **epic-id** or **all** (required): Scope of the review
- **--owner=name** (optional): Filter by action owner name

## Output format

```markdown
# Action Items Review

**Date**: [today]
**Scope**: [epic name(s)]
**Filter**: [owner filter if applied, or "All owners"]
**Total open items**: [count]

## Overdue ([count] items)

| Epic | Action | Owner | Due Date | Days Overdue |
|---|---|---|---|---|
| [epic] | [action text] | [owner] | [date] | [N days] |

## Due This Week ([count] items)

| Epic | Action | Owner | Due Date | Days Left |
|---|---|---|---|---|
| [epic] | [action text] | [owner] | [date] | [N days] |

## Upcoming ([count] items)

| Epic | Action | Owner | Due Date |
|---|---|---|---|
| [epic] | [action text] | [owner] | [date] |

## No Due Date ([count] items)

| Epic | Action | Owner | Source |
|---|---|---|---|
| [epic] | [action text] | [owner] | [source] |

## Summary

- [N] items overdue — immediate attention needed
- [N] items due this week
- [N] items with no due date — consider adding deadlines
```

## Rules

- Only show unchecked items (skip completed `- [x]` items).
- Parse due dates from the action item text (look for patterns like "Due: YYYY-MM-DD", "due date: ...", "by [date]").
- Parse owner from the action item text (look for "Owner: [name]", "— [name]").
- If an epic has no `action-items.md` file, skip it silently.
- If no open items are found, report that clearly.
- Omit empty sections (e.g., if no items are overdue, skip that section).
