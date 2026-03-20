# /daily-brief

Generate a morning briefing with today's calendar events, epic priorities, critical deadlines, and suggested focus.

## Usage
`/daily-brief`

## What this skill does

1. Reads `knowledge/calendar-config.md` for the ICS URL
   - If ICS URL present: fetches calendar via WebFetch and parses events for today
   - If no ICS URL: reads `knowledge/calendar-events.md` as manual fallback
2. Reads `epic-context.md` from every epic directory under `epics/` (skips `_template` and `_example`)
3. Reads `action-items.md` from every epic to find overdue and due-today items
4. Reads `open-questions.md` from every epic for blocker count
5. Calculates priority scores for all epics using the Mode 5 algorithm
6. Delegates to `pm-support` agent (daily/weekly planning mode)
7. Saves output to `briefings/YYYY-MM-DD.md`
8. Also displays the briefing in the console

## Input

No parameters required. Uses today's date automatically.

## Output format

```markdown
# Daily Briefing — [Date]

## Today's Calendar

- [Time] - [Event title] (Epic: [epic-id if matched])

## Top Priorities (by score)

1. [epic-id] — [Recommended action] (Score: [N]/100)
   Urgency: [N] | Dependencies: [N] | Stakeholder: [N] | Sprint: [N] | Questions: [N]
2. [epic-id] — [Recommended action] (Score: [N]/100)
3. [epic-id] — [Recommended action] (Score: [N]/100)

## Critical Deadlines

- [epic-id] [Deadline type]: [Date] ([N] days)

## Overdue Action Items

- [N] items overdue across [N] epics — run `/action-review all` for details

## Suggested Focus

- Morning: [Action based on calendar + priorities]
- Afternoon: [Action based on calendar + priorities]
```

## Rules

- If no calendar events are found for today, show "No calendar events for today" and skip that section.
- If no ICS URL and no manual events, note: "No calendar data available. Configure ICS URL in knowledge/calendar-config.md or add events to knowledge/calendar-events.md."
- Always calculate priority scores even without calendar data (urgency dimension uses available deadline info from epic-context.md).
- Suggested focus should reference specific skills when applicable (e.g., "Run `/workshop-followup`", "Run `/sprint-prep`").
- Save the briefing to `briefings/YYYY-MM-DD.md`. If the file already exists (re-run), overwrite it.
- Omit empty sections.

## Delegate to

Use the `pm-support` agent (daily/weekly planning mode).
