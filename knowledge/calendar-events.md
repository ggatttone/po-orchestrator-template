# Calendar Events — Manual Fallback

This file is the manual fallback when no ICS calendar URL is configured in `knowledge/calendar-config.md`.

Add upcoming events in YAML format. The `pm-support` agent parses this file for daily briefs and weekly plans.

## Format

```yaml
events:
  - date: "2026-03-25"
    time: "09:00"
    duration: "1h"
    title: "Sprint Planning — Order Processing"
    epic: "order-processing"
    type: "sprint-planning"

  - date: "2026-03-26"
    time: "14:00"
    duration: "2h"
    title: "Workshop: Inventory Requirements"
    epic: "inventory-mgmt"
    type: "workshop"

  - date: "2026-03-28"
    time: "10:00"
    duration: "1h"
    title: "Sprint Review"
    epic: "all"
    type: "sprint-review"
```

## Event types

| Type | Description |
|---|---|
| `sprint-planning` | Sprint planning ceremony |
| `sprint-review` | Sprint review / demo |
| `backlog-refinement` | Backlog refinement session |
| `standup` | Daily standup |
| `workshop` | Requirements or design workshop |
| `stakeholder-alignment` | Stakeholder meeting |
| `stagegate` | Stagegate review |
| `other` | Any other meeting |

## Current Events

```yaml
events: []
```
