# Shared Rules — All Agents

> Every sub-agent must read this file before starting work. These rules apply universally.

## General Instructions
Execute first, plan minimally. Do not enter extended planning or clarification loops — if the task is clear enough to start, start. Flag uncertainties inline rather than blocking on them.

## Language
<!-- CUSTOMIZE: If you communicate in a language other than English, add common command phrases here.
     Example (Italian):
     User communicates in both English and Italian. Understand Italian commands like 'riprendi' (resume),
     'prosegui' (continue), 'uno alla volta' (one at a time), 'cosa c'è in programma' (what's on the agenda). -->

## Data integrity
- Never invent requirements, information, data, or scenarios.
- Only document what is stated or clearly implied in the source material.
- If information is missing, create an "Assumptions" or "Open Questions" section — do not fill gaps with guesses.
- Never fabricate progress data, velocity numbers, sprint metrics, or priority scores.

## Facts vs interpretation
- Separate facts from interpretation.
- If you interpret something, mark it: "**Interpretation**: ..."

## Terminology
- Use terms from `knowledge/glossary.md`. See `knowledge/glossary.md` § "How to use glossary terms" for the inline-expansion rule on first occurrence.
- If a new term appears, flag it for addition to the glossary and to the Confluence Space-level Glossary (see `knowledge/atlassian-conventions.md` § 10).

## Output standards
- Always write in English.
- Use simple sentences. Assume a non-technical audience.
- Be structured: headings, bullets, short paragraphs.
- Omit empty sections entirely rather than leaving them empty.

## Epic context
- When working on an epic, always read `epics/{epic-id}/epic-context.md` first.
- For "all" scope, skip the `_template` and `_example` directories.
- When Jira data is available, prefer it over local files for story status and sprint information.

## Confluence update protocol
Before calling `confluence_update_page`:
1. Show the user the planned HTML changes (key sections, not full HTML dump).
2. Confirm both page-level layout (full-width) AND section-level formatting.
3. Reference `knowledge/atlassian-conventions.md` § 7 for canonical table formats.
4. Only push after explicit user approval.

## Confluence
When working with Confluence pages, always verify both page-level layout settings AND internal section formatting. Test the final result against the user's reference page before reporting completion.

## Workflow Rules
Process items one at a time unless explicitly told to parallelize. Never start multiple epics, batches, or large operations simultaneously.

## Tool Fallbacks
When Atlassian MCP is unavailable, immediately fall back to REST API via curl without extended troubleshooting. Keep Jira/Confluence REST API patterns documented in `knowledge/jira-field-rules.md` § 10 for reference.

## Documentation Standards
When updating project documents (epics, action items, meeting notes), always verify: correct domain/person ownership attribution, exclude already-resolved action items, and confirm epic-to-requirement mapping before writing.

## Quality check (before any final output)
- Scope is clear (in scope vs out of scope).
- Each acceptance criterion can be tested.
- Open questions are visible and grouped.
- Terminology matches your organisation's glossary.
- No content was invented — all data points are sourced from actual files, Jira, or Confluence.
