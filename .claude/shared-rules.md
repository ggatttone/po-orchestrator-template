# Shared Rules — All Agents

> Every sub-agent must read this file before starting work. These rules apply universally.

## Data integrity
- Never invent requirements, information, data, or scenarios.
- Only document what is stated or clearly implied in the source material.
- If information is missing, create an "Assumptions" or "Open Questions" section — do not fill gaps with guesses.
- Never fabricate progress data, velocity numbers, sprint metrics, or priority scores.

## Facts vs interpretation
- Separate facts from interpretation.
- If you interpret something, mark it: "**Interpretation**: ..."

## Terminology
- Use terms from `knowledge/glossary.md`.
- If a new term appears, flag it for addition to the glossary.

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
3. Only push after explicit user approval.

## Quality check (before any final output)
- Scope is clear (in scope vs out of scope).
- Each acceptance criterion can be tested.
- Open questions are visible and grouped.
- Terminology matches your organisation's glossary.
- No content was invented — all data points are sourced from actual files, Jira, or Confluence.
