# /new-story

Create a Jira-ready user story from an existing requirement.

## Usage
`/new-story [epic-id] [requirement-id]`

## What this skill does

1. Reads the specified requirement from `epics/{epic-id}/requirements-log.md`
2. Reads `knowledge/naming-conventions.md` for Jira naming rules (when available)
3. Reads `knowledge/glossary.md` for correct terminology
4. Creates one or more user stories from the requirement:
   - Summary (short, action-oriented)
   - Description (As a / I want / So that + context + assumptions)
   - Acceptance Criteria (derived from requirement ACs)
   - Epic key, labels, stakeholders
   - Traceability to source requirement
5. Updates `epics/{epic-id}/story-index.md` with the new stories

## Input

The user provides:
- The epic ID (directory name under `epics/`)
- The requirement ID (e.g., REQ-WIM-001)
- Optionally: additional context or constraints

## Rules

- Every requirement acceptance criterion must be covered by at least one story criterion.
- Stories must be sprint-sized. Break large requirements into multiple stories.
- Maintain traceability (story -> requirement).
- Use business language, not technical jargon.
- Output in a format that can be pasted directly into Jira.

## Delegate to

Use the `jira-story-engineer` agent for the actual story creation work.
