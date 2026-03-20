# /status-update

Produce an audience-tailored status update draft for one or all epics.

## Usage
`/status-update [audience] [epic-id|all]`

Where `audience` is one of:
- `steering-group` — executive summary, 1 page max
- `program-board` — professional, detailed, 1-2 pages
- `bo-community` — collaborative, business-focused, 1 page
- `core-team` — direct, operational, 0.5-1 page

## What this skill does

1. Validates the audience parameter against the 4 known audiences
2. If `all`: scans all epic directories under `epics/` (skips `_template` and `_example`)
3. If specific epic-id: reads that epic's files
4. Reads `knowledge/governance.md` for audience expectations
5. Reads `knowledge/glossary.md` for terminology
6. Optionally: Queries Jira via Atlassian MCP for latest sprint data
7. Delegates to `stakeholder-comms` agent for drafting
8. Outputs the status update draft

## Input

The user provides:
- **audience** (required): One of `steering-group`, `program-board`, `bo-community`, `core-team`
- **epic-id** or **all** (required): Scope of the update
- **--period [date-range]** (optional): Reporting period (e.g., `2026-02-01..2026-02-14`)

## Output format

```markdown
# Status Update: [Epic Name / Program Overview]

**Date**: [today]
**Audience**: [Steering Group / Program Board / BO Community / Core Team]
**Period**: [sprint or date range]
**Author**: [Your Name], Product Owner

---

## Executive Summary

[2-3 sentences tailored to audience level]

## Progress

| Item | Status | Notes |
|---|---|---|

## Key Decisions Made

- [Decision] — [Date] — [Impact]

## Risks & Blockers

| Risk/Blocker | Impact | Mitigation | Owner |
|---|---|---|---|

## Decisions Needed [REVIEW]

- [Decision needed] — [Context] — [Deadline]

## Next Steps

- [Action] — [Owner] — [Timeline]

---
*Prepared by PO Operations Agent System. Review by Product Owner required before distribution.*
```

## Rules

- If audience is not recognized, list the 4 valid options and ask the user to choose.
- For `all` scope, produce a cross-epic summary — not individual epic reports.
- Mark items needing the PO's personal input with `[REVIEW]`.
- Never include internal technical details in `steering-group` or `bo-community` outputs.
- Omit empty sections (e.g., if no risks, skip Risks & Blockers).
- Output must be directly pasteable into email, Teams message, or Confluence page.
- When Jira data is available, prefer it for story counts and sprint progress.

## Delegate to

Use the `stakeholder-comms` agent.
