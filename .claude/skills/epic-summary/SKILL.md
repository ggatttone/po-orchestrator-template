# /epic-summary

Produce a summary report for one or all epics.

## Usage
`/epic-summary [epic-id|all]`

## What this skill does

1. If specific epic-id: reads all 6 files in `epics/{epic-id}/`
2. If `all`: reads `epic-context.md` from every epic directory under `epics/` (skips `_template` and `_example`)
3. Reads `knowledge/glossary.md` for terminology
4. Optionally: Queries Jira via Atlassian MCP for story counts and statuses
5. Delegates to `stakeholder-comms` agent for formatting
6. Produces the summary report

## Input

The user provides:
- **epic-id** or **all** (required): Scope of the summary
- **--format [brief|detailed]** (optional): Level of detail
  - Default for single epic: `detailed`
  - Default for `all`: `brief`

## Output format (single epic, detailed)

```markdown
# Epic Summary: [Epic Name]

**Date**: [today]
**Epic**: [epic-id] ([Jira key])
**Status**: [from epic-context]
**Development Block**: [from epic-context]

## Objective

[From epic-context.md]

## Progress Snapshot

| Metric | Count |
|---|---|
| Requirements | [N] |
| User Stories | [N] |
| Open Questions | [N] |
| Open Action Items | [N] |
| Decisions Made | [N] |

## Recent Decisions (last 5)

- [Decision summary]

## Top Risks / Blockers

- [Overdue items, blocking questions]

## Dependencies

- [Related epics from epic-context.md]
```

## Output format (all epics, brief)

```markdown
# Epic Portfolio Summary

**Date**: [today]
**Total Epics**: [N]

| Epic | Jira Key | Status | Reqs | Stories | Open Qs | Actions | Last Updated |
|---|---|---|---|---|---|---|---|

## Attention Needed

- [Epics with overdue items or stale contexts]
```

## Rules

- For `all` scope, always skip the `_template` and `_example` directories.
- If an epic directory has only template content (no real data), mark it as "Not started" in the portfolio table.
- Counts come from parsing the actual files:
  - Requirements: count `REQ-` entries in requirements-log.md
  - Stories: count story entries in story-index.md
  - Open questions: count entries in open-questions.md
  - Action items: count unchecked `- [ ]` items in action-items.md
  - Decisions: count entries in decisions-log.md
- Sort the portfolio table by status (In Progress first, then by last updated date).
- When Jira data is available and differs from local files, note the discrepancy.
- Omit empty sections.

## Delegate to

Use the `stakeholder-comms` agent.
