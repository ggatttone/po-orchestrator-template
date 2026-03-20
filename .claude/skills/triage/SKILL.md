# /triage

Run the full 7-step epic triage process on an epic — Jira Spikes, OQ/AI consolidation, Confluence sync, escalation summary.

## Usage
`/triage [epic-id]`

## What this skill does

Executes the standardised triage methodology on the specified epic. Process items ONE AT A TIME — do not batch all steps into a single output.

### Step 1: Source Sync & Jira Spike Creation

1. **Read local files**: open-questions.md, action-items.md, decisions-log.md, story-index.md, requirements-log.md
2. **Read Confluence page** (via MCP): compare OQ count, decision count, requirements count
3. **Query Jira** (via MCP): get actual children of the epic (`parent = EPIC-KEY`), identify phantom keys in local story-index
4. **Triage each open question**: classify as Still Open / Direction set / Answered / Cross-epic / Deferred
5. **Triage each action item**: classify as Still Open / Done / OVERDUE / Stale / Duplicate
6. **Group related OQs + AIs by topic** -> create topic-based Jira **Spikes**
   - Spike fields: `parent` = epic key, `assignee` = epic Business Owner, `priority`, `description` (with context + scenarios + what needs resolution), acceptance criteria checklist
   - Responsible persons noted in description, NOT as assignee
   - See `knowledge/jira-field-mapping.md` for issue type ID and custom field IDs

### Step 2: Consolidate OQ + AI -> open-questions.md

Restructure open-questions.md to new format:
```
| Q-ID | Question | Owner | Due | Jira Spike | Status | Decision/Note |
```
- Where an AI is the resolution path for an OQ, merge AI info into the OQ row
- Add Jira Spike links
- Mark answered questions with decision references
- Mark OVERDUE/STALE items with bold formatting

### Step 3: Clean up action-items.md

- Keep only standalone AIs (no matching OQ)
- Mark Done/Stale items with status
- Add "Merged into open-questions.md" section listing which AI merged into which OQ

### Step 4: Rebuild story-index.md

- Query Jira for actual children (`parent = EPIC-KEY ORDER BY key ASC`)
- Replace ALL phantom keys with actual Jira issues
- Organize by section: Business Requirement -> Requirements (REQ-*) -> Open Question Spikes (OQ)

### Step 5: Confluence Sync

For each Confluence page:
1. Add missing decisions (from local decisions-log.md)
2. Update OQ table to include Jira Spike link column
3. Add missing OQs
4. Mark answered OQs
5. Use `confluence_update_page` via MCP — preserve all existing content
6. **Follow the Confluence update protocol** from shared-rules.md (show changes, get approval before pushing)

### Step 6: Cross-Epic Cleanup

- Identify duplicate items across epics -> add cross-references in OQ files
- Assign single owner per cross-epic blocker

### Step 7: Escalation Summary

Generate escalation summary with:
- OVERDUE items (immediate escalation)
- STALE items (follow-up required)
- Items due this week
- Cross-epic blockers
- Summary statistics
- Recommended next steps

## Input

The user provides:
- **epic-id** (required): The epic directory name under `epics/`

## Key Rules

- **Assignee**: Always the epic's Business Owner (single accountable person). Topic experts go in description as "Responsible: [name]"
- **Spike type**: See `knowledge/jira-field-mapping.md` for the Spike issue type ID
- **Naming**: Spike summary = "OQ: " + topic title
- **Commit**: Stage only triage-related files, not unrelated changes
- **One at a time**: Present the triage plan first, then execute steps sequentially with user confirmation

## Delegate to

Use the `pm-support` agent.
