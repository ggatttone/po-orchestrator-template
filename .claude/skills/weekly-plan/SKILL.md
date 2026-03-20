# /weekly-plan

Generate a weekly planning overview with this week's calendar, epic priorities, sprint status, and recommended focus areas.

## Usage
`/weekly-plan`

## What this skill does

1. Reads `knowledge/calendar-config.md` for the ICS URL
   - If ICS URL present: fetches calendar via WebFetch and parses events for the current week (Monday-Friday)
   - If no ICS URL: reads `knowledge/calendar-events.md` as manual fallback
2. Reads `epic-context.md` from every epic directory under `epics/` (skips `_template` and `_example`)
3. Reads `action-items.md` from every epic for open item counts
4. Reads `open-questions.md` from every epic for blocker counts
5. Calculates priority scores for all epics using the Mode 5 algorithm
6. Reads `knowledge/way-of-working.md` for sprint boundary info
7. Delegates to `pm-support` agent (daily/weekly planning mode)
8. Saves output to `briefings/week-YYYY-WW.md`
9. Also displays the plan in the console

## Input

No parameters required. Uses the current week automatically.

## Output format

```markdown
# Weekly Plan — Week [N], [Date Range]

## This Week's Calendar

| Day | Time | Event | Epic |
|---|---|---|---|

## Epic Priority Ranking

| Rank | Epic | Score | Key Action This Week |
|---|---|---|---|

## Sprint Status

- Sprint: [name/number if known]
- Days remaining: [N]
- Key ceremonies this week: [list]

## Critical Deadlines (Next 14 Days)

| Deadline | Epic | Type | Days Left |
|---|---|---|---|

## Open Action Items Summary

- Total open: [N] across [N] epics
- Overdue: [N]
- Due this week: [N]

## Recommended Weekly Focus

1. [Priority action for the week]
2. [Priority action for the week]
3. [Priority action for the week]
```

## Rules

- Week runs Monday to Friday (European business week).
- Critical deadlines section looks 14 days ahead (not just this week).
- If sprint dates are unknown, note "Sprint dates not available" and skip the Sprint Status section.
- Save the plan to `briefings/week-YYYY-WW.md` where WW is the ISO week number. Overwrite if re-run.
- Omit empty sections.

## Delegate to

Use the `pm-support` agent (daily/weekly planning mode).
