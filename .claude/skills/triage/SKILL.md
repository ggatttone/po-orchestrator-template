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
6. **Classify each OQ for spike-worthiness** (OQ/Spike convention — see `knowledge/atlassian-conventions.md` § 1):
   - **Spike-worthy** (active investigation): business confirmation, specialist input (legal/finance/customs), workshop/session topic, cross-epic decision, operational change with concrete impact, new scope discovery
   - **NOT spike-worthy** (design-phase): "How does [Your ERP] model X?", storage model questions, scalability/performance concerns, implementation design — these are answered by the implementation partner within REQ stories during design phase, NOT as standalone spikes
7. **Group spike-worthy OQs by topic** → create topic-based Jira **Spikes**
   - **Default to 1:1** (1 spike : 1 OQ) unless clear topical grouping justifies bundling — bundling complicates closure (see `knowledge/atlassian-conventions.md` § 2)
   - Spike fields: `parent` = epic key, `assignee` = epic Business Owner, `priority`, `description` (ADF with context + scenarios + what needs resolution), Acceptance Criteria custom field (checklist)
   - Responsible persons noted in description, NOT as assignee
   - Use `jira_batch_create_issues` or parallel `jira_create_issue` calls
   - For design-phase OQs (NOT spike-worthy): leave OQ visible in Confluence with status as appropriate, but DO NOT create standalone spike — the resolution will flow via the linked REQ during the design phase

### Step 2: Consolidate OQ + AI → open-questions.md

Restructure open-questions.md to the 8-column format:
```
| Q-ID | Question | Business Impact | Owner | Due | Jira Spike | Status | Decision/Note |
```
- **Business Impact**: For each OQ, write a 1-2 sentence impact statement answering "If this stays unanswered, what breaks or gets delayed for which team?" (see `knowledge/requirement-standards.md` > "Open Questions format")
- **Plain language**: Rewrite questions using business context before technical detail
- Where an AI is the resolution path for an OQ, merge AI info into the OQ row
- Add Jira Spike links as `[PRJ-XXXX](https://YOUR-INSTANCE.atlassian.net/browse/PRJ-XXXX)`
- Mark answered questions with decision references
- Mark OVERDUE/STALE items with bold formatting

### Step 3: Clean up action-items.md

- Keep only standalone AIs (no matching OQ)
- Mark Done/Stale items with status
- Add "Merged into open-questions.md" section listing which AI merged into which OQ

### Step 4: Rebuild story-index.md

- Query Jira for actual children (`parent = EPIC-KEY ORDER BY key ASC`)
- Replace ALL phantom keys with actual Jira issues
- Organize by section: Business Requirement → Requirements (REQ-*) → Open Question Spikes (OQ)

### Step 5: Confluence Sync

For each Confluence page:
1. Add missing decisions (from local decisions-log.md)
2. Update OQ table to include Jira Spike link column
3. Add missing OQs
4. Mark answered OQs
5. Use `confluence_update_page` via MCP — preserve all existing content
6. **Follow the Confluence update protocol** from shared-rules.md (show changes, get approval before pushing)

### Step 6: Cross-Epic Cleanup

- Identify duplicate items across epics → add cross-references in OQ files
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
- **Naming**: Spike summary = "OQ: " + topic title (per `knowledge/atlassian-conventions.md` § 13)
- **Confluence links**: Use `[PRJ-XXXX|https://YOUR-INSTANCE.atlassian.net/browse/PRJ-XXXX]` in Confluence wiki format
- **Commit**: Stage only triage-related files, not unrelated changes
- **One at a time**: Present the triage plan first, then execute steps sequentially with user confirmation

## Delegate to

Use the `pm-support` agent.
