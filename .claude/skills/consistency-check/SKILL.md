# /consistency-check

Check an epic's files for internal consistency, coverage gaps, and quality issues.

## Usage
`/consistency-check [epic-id]`

## What this skill does

1. Reads all files in `epics/{epic-id}/`:
   - `epic-context.md` — scope and objectives
   - `requirements-log.md` — all requirements
   - `story-index.md` — all user stories
   - `decisions-log.md` — all decisions
   - `open-questions.md` — all open questions
   - `action-items.md` — all action items
2. Reads `knowledge/glossary.md` for terminology validation
3. Performs consistency checks across all files
4. Outputs an issues report with severity levels

## Checks performed

### Coverage Analysis
- **Requirements -> Stories**: Does every requirement have at least one linked story?
- **Stories -> Requirements**: Does every story trace back to a requirement?
- **Acceptance criteria coverage**: Are all requirement ACs covered by story ACs?

### Linkage Checks
- **Orphan decisions**: Decisions not referenced by any requirement or question
- **Orphan questions**: Questions not linked to a requirement or decision
- **Dangling references**: Story references to non-existent requirement IDs
- **Missing epic context fields**: Empty fields in epic-context.md

### Staleness Checks
- **Stale open questions**: Questions open for more than 30 days
- **Overdue action items**: Action items past their due date
- **Outdated epic context**: `last updated` field more than 14 days old

### Terminology Check
- **Non-glossary terms**: Key business terms in requirements that are not in `knowledge/glossary.md`
- **Inconsistent naming**: Same concept referred to by different names within the epic

### Completeness Check
- **Missing sections**: Requirements without acceptance criteria, scenarios, or stakeholders
- **Empty files**: Epic files that exist but have no content beyond the template

## Output format

```markdown
# Consistency Check: [Epic Name]

**Date**: [today]
**Epic**: [epic-id] ([Jira key])
**Files checked**: [count]

## Summary

| Severity | Count |
|---|---|
| Critical | [N] |
| Warning | [N] |
| Info | [N] |

## Critical Issues ([N])

Issues that block development readiness or indicate data problems.

| # | Check | Issue | Location | Recommendation |
|---|---|---|---|---|
| 1 | [Check name] | [Issue description] | [File:section] | [What to do] |

## Warnings ([N])

Issues that should be addressed but are not blockers.

| # | Check | Issue | Location | Recommendation |
|---|---|---|---|---|
| 1 | [Check name] | [Issue description] | [File:section] | [What to do] |

## Info ([N])

Observations and suggestions for improvement.

| # | Check | Observation | Location |
|---|---|---|---|
| 1 | [Check name] | [Observation] | [File:section] |

## Coverage Summary

| Metric | Value |
|---|---|
| Total requirements | [N] |
| Requirements with stories | [N] |
| Coverage | [N%] |
| Total stories | [N] |
| Stories with requirement links | [N] |
| Orphan stories | [N] |

## Recommendations

1. [Specific, actionable recommendation]
2. [Specific, actionable recommendation]
```

## Rules

- Read all files in read-only mode — never modify epic files during a consistency check.
- Report only factual findings, not opinions.
- Severity definitions:
  - **Critical**: Missing traceability, broken references, requirements without ACs
  - **Warning**: Stale questions (>30 days), missing stakeholders, incomplete sections
  - **Info**: Style suggestions, glossary additions, minor improvements
- If a file is empty or uses only the template content, report it as "Info: Template only, no content added yet."
- Omit empty severity sections.
