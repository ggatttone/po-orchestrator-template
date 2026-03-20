# /new-requirement

Create a new structured requirement with context, baseline analysis, Jira story, and Confluence update.

## Usage
`/new-requirement [epic-id] [requirement-title]`

## What this skill does

### Phase 1: Extract & Structure
1. Reads the specified epic's context from `epics/{epic-id}/epic-context.md`
2. Reads `knowledge/requirement-standards.md` for the format template
3. Reads `knowledge/glossary.md` for correct terminology
4. Creates a new requirement following the section-per-requirement structure:
   - Requirement ID (auto-generated: REQ-[EPIC-SHORT]-[NNN])
   - Title
   - **Context** (why it exists + baseline analysis + cross-epic dependencies)
   - User Story (As a / I want / So that)
   - Description
   - Scenarios (Given/When/Then, with origin tags and source quotes)
   - Acceptance Criteria (testable, checkbox format)
   - Open Questions (with context per question)
   - Decisions (with context per decision)
   - Stakeholders

### Phase 2: Analyse & Enrich
5. **Baseline analysis**: Reads `knowledge/systems-landscape.md` and assesses what the platform already supports. Classifies as OOTB/Config/Custom Objects/Custom Logic/Not Supported.
6. **Cross-epic dependency scan**: Reads other active epics' requirements-logs to identify shared requirements, overlapping scenarios, or blocking dependencies.

### Phase 3: Write Locally
7. Appends the requirement to `epics/{epic-id}/requirements-log.md` (summary table + detail section)
8. Adds any open questions to `epics/{epic-id}/open-questions.md` (with context)
9. Adds any decisions to `epics/{epic-id}/decisions-log.md` (with context)

### Phase 4: Jira & Confluence
10. **Creates a Jira story** under the epic via `jira_create_issue` (always automatic):
    - Issue type: Story, Parent: epic key from epic-context.md
    - Summary: user story title
    - Description: full content (context + user story + scenarios + ACs)
11. **Updates `story-index.md`** with the new Jira story key
12. **Updates Confluence page** (if linked in epic-context.md):
    - Appends new H2 requirement section
    - Adds row to summary table
    - Re-sets full-width properties after update

## Input

The user provides:
- The epic ID (directory name under `epics/`)
- A title or topic for the requirement
- Optionally: raw text, notes, or context to base the requirement on

## Rules

- Never invent information. If details are missing, add them as Open Questions with context.
- Mark interpretations explicitly.
- Use terminology from the glossary.
- Follow the section-per-requirement format in `knowledge/requirement-standards.md` exactly.
- Every requirement must have a Context section with baseline analysis and dependency scan.
- Every open question and decision must have a context annotation.
- Jira story creation is always automatic — no prompt, no flag.

## Delegate to

Use the `requirements-analyst` agent for the actual requirement creation work.
