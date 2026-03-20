# /timeline-sync

Synchronise calendar events with epic context files. Reads calendar data and flags or updates epic deadlines accordingly.

## Usage
`/timeline-sync`

## What this skill does

1. Reads `knowledge/calendar-config.md` for the ICS URL
   - If ICS URL present: fetches calendar via WebFetch and parses all upcoming VEVENT entries (next 30 days)
   - If no ICS URL: reads `knowledge/calendar-events.md` as manual fallback
2. Reads `epic-context.md` from every epic directory under `epics/` (skips `_template` and `_example`)
3. Matches calendar events to epics by looking for:
   - Epic directory names in event titles
   - Jira keys in event titles
   - Epic names or keywords in event descriptions
4. Compares calendar deadlines with epic-context.md data
5. Delegates to `pm-support` agent (daily/weekly planning mode)
6. Reports findings and suggests updates

## Input

No parameters required. Scans all epics and all upcoming calendar events (next 30 days).

## Output format

```markdown
# Timeline Sync Report

**Date**: [today]
**Calendar source**: [ICS URL / Manual fallback / None]
**Epics scanned**: [N]
**Events found (next 30 days)**: [N]

## Events Matched to Epics

| Event | Date | Epic | Type | Status |
|---|---|---|---|---|
| [Event title] | [Date] | [epic-id] | [stagegate/sprint/workshop/other] | [New / Already tracked / Updated] |

## Unmatched Events

Events that could not be matched to any epic:
- [Date] - [Event title] — *Consider tagging with epic name or Jira key*

## Suggested Epic Context Updates

The following epic-context.md files may need updating:

1. **[epic-id]**: New stagegate deadline detected ([Date]) — not yet in epic-context.md
2. **[epic-id]**: Sprint review date changed ([Old] -> [New])

## Conflicts Detected

| Epic A | Epic B | Conflict | Date |
|---|---|---|---|
| [epic] | [epic] | [Both have stagegate on same day] | [Date] |

## Summary

- [N] events matched to epics
- [N] events unmatched
- [N] suggested updates
- [N] conflicts detected
```

## Rules

- This skill REPORTS findings but does NOT automatically modify epic-context.md files. It suggests changes for the user to review.
- Calendar events are matched to epics using fuzzy matching on titles (case-insensitive, partial match).
- If no calendar data is available (no ICS URL, no manual events), report clearly and suggest configuring the ICS URL.
- Skip `_template` and `_example` directories when scanning epics.
- Omit empty sections (e.g., no conflicts detected).

## Delegate to

Use the `pm-support` agent (daily/weekly planning mode).
