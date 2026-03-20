# /sprint-prep

Assess story readiness for an upcoming sprint and produce a sprint readiness report.

## Usage
`/sprint-prep [epic-id]`

## What this skill does

1. Reads the epic context from `epics/{epic-id}/epic-context.md`
2. Reads `epics/{epic-id}/story-index.md` for current stories
3. Reads `epics/{epic-id}/open-questions.md` for blocking questions
4. Reads `epics/{epic-id}/action-items.md` for overdue items
5. Reads `knowledge/way-of-working.md` for Definition of Ready criteria
6. Optionally: Queries Jira via Atlassian MCP for sprint-specific data (current sprint stories, points, status)
7. Delegates to `pm-support` agent for sprint preparation analysis
8. Outputs the sprint readiness report

## Input

The user provides:
- **epic-id** (required): The epic directory name under `epics/`
- **--sprint [name/number]** (optional): Specify which sprint to assess

## Output format

```markdown
# Sprint Readiness: [Epic Name]

**Date**: [today]
**Sprint**: [sprint name/number, if known]
**Epic**: [epic-id] ([Jira key])

## Readiness Summary

| Metric | Value |
|---|---|
| Stories assessed | [N] |
| Ready (meets DoR) | [N] |
| Not ready | [N] |
| Blocked | [N] |

## Ready Stories

| Story | Size | ACs | Dependencies | Notes |
|---|---|---|---|---|

## Not Ready Stories

| Story | Missing DoR Items | Recommendation |
|---|---|---|

## Blockers

| Item | Type | Owner | Impact |
|---|---|---|---|

## Recommendations

1. [Specific, actionable recommendation]
```

## Definition of Ready criteria

Each story is assessed against these 5 criteria (from `knowledge/way-of-working.md`):

1. Description and acceptance criteria are clear
2. Dependencies are identified and resolved (or planned)
3. Story is estimated
4. Test scenarios are outlined
5. No blocking open questions remain

A story is "Ready" only when all 5 criteria are met. If any criterion fails, the story is "Not ready" with the missing items listed.

## Rules

- Always cite the 5 DoR criteria when assessing stories.
- Flag stories that have unresolved open questions (from `open-questions.md`) as "Blocked".
- Flag overdue action items (from `action-items.md`) that impact the upcoming sprint.
- If no stories exist in the epic, report clearly: "No stories found in story-index.md. Consider running `/new-story` to create stories from requirements."
- When Jira data is available and differs from local files, note the discrepancy.
- Omit empty sections.

## Delegate to

Use the `pm-support` agent (sprint preparation mode).
