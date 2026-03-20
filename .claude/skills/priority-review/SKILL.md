# /priority-review

Calculate and display priority scores for one or all epics using a multi-dimensional scoring algorithm.

## Usage
`/priority-review [epic-id|all]`

## What this skill does

1. If specific epic-id: reads all files in `epics/{epic-id}/`
2. If `all`: reads `epic-context.md`, `open-questions.md`, and `action-items.md` from every epic under `epics/` (skips `_template` and `_example`)
3. Reads calendar events (ICS URL from `knowledge/calendar-config.md` or fallback `knowledge/calendar-events.md`)
4. Optionally: Queries Jira via Atlassian MCP for story counts and sprint status
5. Delegates to `pm-support` agent (priority analysis mode)
6. Calculates priority score (0-100) per epic using 5 dimensions:
   - **Urgency (0-30)**: Days until deadline x deadline type
   - **Dependencies (0-25)**: Cross-epic blocking relationships
   - **Stakeholder Impact (0-20)**: Stakeholder seniority and breadth
   - **Sprint Alignment (0-15)**: Current sprint status and story readiness
   - **Open Questions (0-10)**: Fewer blockers = higher score
7. Displays ranked output in console
8. When scope is `all`: also writes compact ranking to `knowledge/epic-timeline.md`

## Input

The user provides:
- **epic-id** or **all** (required): Scope of the review

## Output format (single epic)

```markdown
# Priority Analysis: [Epic Name]

**Date**: [today]
**Epic**: [epic-id] ([Jira key])
**Priority Score**: [N]/100

| Dimension | Score | Max | Reasoning |
|---|---|---|---|
| Urgency | [N] | 30 | [Nearest deadline] |
| Dependencies | [N] | 25 | [Blocking status] |
| Stakeholder Impact | [N] | 20 | [Key stakeholders] |
| Sprint Alignment | [N] | 15 | [Sprint status] |
| Open Questions | [N] | 10 | [Question count] |
| **Total** | **[N]** | **100** | |

## Recommended Action

[Specific recommendation based on score analysis]
```

## Output format (all epics)

```markdown
# Priority Analysis: All Epics

**Date**: [today]
**Epics analysed**: [N]

## Priority Ranking

| Rank | Epic | Jira Key | Score | Urgency | Deps | Stakeholder | Sprint | Questions |
|---|---|---|---|---|---|---|---|---|

## Top 3 Recommended Actions

1. [Epic] — [Action] (Score: [N])
2. [Epic] — [Action] (Score: [N])
3. [Epic] — [Action] (Score: [N])

## Cross-Epic Dependencies

| Epic A | Epic B | Relationship | Impact |
|---|---|---|---|
```

## Rules

- Always show the dimension breakdown, not just the total score.
- When scoring, cite the specific data that drove the score (e.g., "Stagegate in 5 days" for urgency).
- For `all` scope, skip `_template` and `_example` directories.
- When `all` scope: update `knowledge/epic-timeline.md` with the compact ranking.
- If only one epic exists, still show the full scoring table (useful for tracking score changes over time).
- Omit empty sections (e.g., no cross-epic dependencies).

## Delegate to

Use the `pm-support` agent (priority analysis mode).
