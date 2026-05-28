# /consistency-check

Check an epic's files for internal consistency, coverage gaps, **and Atlassian sync state**.

## Usage
`/consistency-check [epic-id]`

Optional flags:
- `--local-only` — skip Atlassian-side checks (faster, but partial)
- `--no-comments` — skip comment scan on Confluence and Jira
- `--cross-epic` — also check related epics flagged in `epic-context.md`
- `--slim` — enforce slim profile checks (REQ/OQ/DEC anti-duplication, see `knowledge/atlassian-conventions.md` § 7.1)
- `--strict` — treat all Warnings as Critical (used during pre-push gate)

## What this skill does

The skill performs a **two-sided check**: local files + live Atlassian state.

### Phase 1 — Local files

Reads all files in `epics/{epic-id}/`:
- `epic-context.md` — scope, objectives, stakeholders
- `requirements-log.md` — all REQ
- `story-index.md` — all linked Jira stories
- `decisions-log.md` — all DEC
- `open-questions.md` — all OQ
- `action-items.md` — all ACT

Plus `knowledge/glossary.md` for terminology validation.

### Phase 2 — Atlassian state (default)

Pulls live state of:
- **Confluence page** referenced in `epic-context.md` or in the epic's `confluence-drafts/` directory
  - Current version + body content
  - **All comments on the page** (date, author, body)
  - Last updated date
- **Jira epic issue**
  - Status, last updated
  - All comments on the epic
- **Jira sub-issues** linked from `story-index.md` — for each:
  - Status, last updated
  - Comments since last local sync (typically: comments newer than the most recent changelog entry for the epic)
- **Jira spike search** to detect orphan or duplicate spikes:
  - JQL: `project = PRJ AND parent = {EPIC-KEY} AND issuetype = Spike AND created >= "{last-sync-date}"`
  - Verifies that all `Q-{EPIC-SHORT}-NNN` referenced in `open-questions.md` have a matching Jira spike (or are flagged as `(new spike TBD)`)

### Phase 3 — Cross-state diff

Compares local state vs Atlassian state and flags divergences.

### Phase 4 — In-content Jira↔Confluence cross-check

For each Jira key embedded in the Confluence page body:

1. Extract all Jira keys via regex (e.g. `\b(PRJ|XYZ)-\d{2,5}\b`) from storage HTML — adapt to your project keys.
2. Bulk-JQL them in one call. `fields` parameter MUST be explicit: `summary, status, resolution, parent, issuetype, customfield_XXXXX` (Story field id — see `knowledge/jira-field-mapping.md`). Without `resolution` in fields, Done+null spikes slip through silently. If the page cites > 40 keys, paginate (`limit=50`); MCP `jira_search` output is capped at 30K chars.
3. For each key, compare:
   - **Status drift**: Confluence cell shows "Open"/"PARTIAL" but Jira status is `Done` / resolution set → CRITICAL "drift reversed: Jira closed, Confluence open". **Downgrade exception**: if the Confluence cell contains an inline annotation matching `<em>\(closed (Done|Won't Do) \d{4}-\d{2}-\d{2}( — design-phase)?\)</em>`, the closure context is already on the page → downgrade to INFO. This pattern is legitimate per `knowledge/atlassian-conventions.md` § 5.
   - **Status drift reverse**: Confluence shows "Answered"/"Closed" but Jira status is `Backlog` / no resolution → CRITICAL "drift reversed: Confluence closed, Jira open".
   - **1:N spike with conflicting cell statuses**: when the same Jira key appears in multiple OQ rows with different status → CRITICAL "1:N spike status conflict". Per § 2, a 1:N spike closes only when all linked OQs are resolvable; mixed status indicates either a missed update or a cell that should be reopened.
   - **Ownership**: parent epic of the Jira key must appear in `CLAUDE.md` § 5 active epics table. If parent is outside active epics, flag WARNING "out-of-scope reference".
   - **Title sync**: if the Confluence cell carries a verbatim quote from the Jira `summary`, check that the quote still matches (titles change).
4. Output an `In-content Drift` block in the report (see Output format below).

### Phase 5 — Slim profile compliance (opt-in via `--slim` flag)

For Confluence pages declared as slim, enforce the column structures from `knowledge/atlassian-conventions.md` § 7.1:

| Table | Expected columns | Anti-pattern flag |
|---|---|---|
| Requirements | `ID \| Title \| Pri \| Jira Issue \| Why on Confluence` | (a) presence of `Status` / `Importance` / `Description` columns → CRITICAL "canonical layout on slim page"; (b) `Why on Confluence` cell empty → CRITICAL "deletion candidate"; (c) `Why on Confluence` cell verbatim restates the title or just says "see Jira" → CRITICAL "duplication, not adding value" |
| Open Questions | `Q-ID \| Question \| Responsible \| Jira Spike \| Status` | (a) presence of `Answer` / `Date` / `Business Impact` columns → CRITICAL "slim drops these — content belongs in Jira Spike"; (b) Question cell > 100 chars → WARNING "condense"; (c) Jira Spike cell empty when Status ≠ Closed → CRITICAL "OQ without Spike on slim page" |
| Decisions | `ID \| Decision \| REQ \| Date \| Rationale` (unchanged) | `Rationale` cell > 25 words → WARNING "narrative belongs in linked Jira issue, not Confluence" |

**Mixed-profile detection**: §7.1 states a page is "either fully slim or fully canonical — never mixed". Resolve each table's profile independently:
- REQ table → *slim* if 5-col `ID|Title|Pri|Jira Issue|Why on Confluence`; *canonical* if it carries `Importance` and/or `Status` columns.
- OQ table → *slim* if 5-col `Q-ID|Question|Responsible|Jira Spike|Status`; *canonical* if 7-col (carries the `REQ` and `Answer` columns).
- DEC table → 5-col in both profiles (not a discriminator).

If the REQ table and the OQ table resolve to **different** profiles on the same page → CRITICAL "mixed table profile — §7.1 requires a page to be fully slim or fully canonical".

### Phase 6 — Structural caps

Quantitative caps measured against the storage HTML:

- Body storage size ≤ 45 KB (slim) / ≤ 80 KB (canonical) → flag WARNING above target, CRITICAL above 2× target.
- `<ac:layout-section>` count: must be exactly 3 — title band `fixed-width` + header `two_equal` + body `single`, in that order (§8). ≠ 3 → WARNING.
- **Layout-section breakout (§8)**: the header `two_equal` and the body `single` `<ac:layout-section>` must carry `ac:breakout-mode="wide"` + `ac:breakout-width="1800"`; the leading `fixed-width` title band keeps `ac:breakout-mode="default"`. A body `single` section without `ac:breakout-mode="wide"` → WARNING "body tables not full-width — add breakout attributes per §8".
- **ToC placement (§8)**: the `toc` macro must sit in the leading `fixed-width` title-band section, immediately after the H1. A `toc` macro positioned after the Objective H2 → WARNING "ToC misplaced — move to the title band per §8".
- `<ac:structured-macro ac:name="status">` per table row: ≤ 1. Any row with > 1 → WARNING "status macro overuse".
- `<ac:structured-macro ac:name="status">` total page count: > 25 on slim, > 50 on canonical → WARNING.
- `<ac:structured-macro ac:name="details">` count: exactly 1 expected. ≠ 1 → INFO.
- `<ac:structured-macro ac:name="expand">` used for non-historical content → INFO "expand intended for historical context".
- Manual `## Changelog` section present in page body → CRITICAL "use native version history instead, see `knowledge/atlassian-conventions.md` § 4".
- Page has no H1 (`<h1>`) → WARNING "page anatomy violation, H1 = Epic Jira title required".
- H3 nesting absent under Assumptions or Processes H2 → INFO "consider adding H3 sub-sections".
- **H2 emoji prefix check (§8)**: every H2 in the canonical set must start with its mandated emoji glyph. A canonical-set H2 with no leading emoji → WARNING. A page where **all** H2 lack their emoji → CRITICAL "emoji prefixes stripped — systematic §8 violation".
- **Canonical H2 completeness (§8)**: the §8 fixed H2 order includes `Success metrics` and `User interaction, design & processes`. If either heading is absent from the page → WARNING.
- **`&harr;` check (§8)**: Confluence storage serialises U+2194 (`↔`) to the entity `&harr;` — this is platform behaviour. Flag `&harr;` inside an `<h2>` that is **not** immediately followed by U+FE0F (variation selector) → WARNING "Decisions H2 emoji renders as plain arrow, not ↔️ — append U+FE0F".
- Date strings matching `\d{4}-\d{2}-\d{2}` hardcoded → WARNING when > 60 occurrences on slim, > 30 on canonical. **Exclude** dates inside `<em>(closed ... YYYY-MM-DD)</em>` annotations from the count.
- Plain-text `@Name` user mentions (regex `@[A-Z][a-z]+(?: [A-Z][a-z]+)+` in `<p>` / `<td>` text, NOT inside `<ac:link><ri:user>` macro) → WARNING. Convert via accountId lookup before push.

### Phase 7 — Editorial / linguistic

Run on visible page text from `<p>` and `<li>` blocks only. **Strip all `<table>`, `<colgroup>`, `<thead>`, `<tbody>` blocks before tokenising** — otherwise tag-stripping concatenates table-row content into single "sentences" and produces false-positive top-N lists.

- Sentence length: split text on `[.!?]`, count words. Sentences > 20 words → WARNING (max 3 listed per page).
- Paragraph length: paragraphs (`<p>` blocks) with > 4 sentences → WARNING.
- Bullet list nesting > 2 levels (`<ul><ul><ul>`) → WARNING "flatten to 2 levels max".
- Acronym first-occurrence expansion check: for each known acronym in `knowledge/glossary.md`, verify first occurrence is in one of the two accepted forms — either `Expansion (ACRONYM)` or `ACRONYM (Expansion)`. Bare `ACRONYM` with no expansion anywhere on the page → WARNING. If the acronym has > 5 occurrences and no expansion at all → CRITICAL.
- Multi-language sync mix in same sentence (heuristic): English clauses containing non-English marker tokens not on the domain term-of-art allowlist (configured in `knowledge/glossary.md`) → WARNING "EN/foreign syntactic mix — preserve term-of-art but rewrite syntax in EN".

### Phase 8 — Jira-side editorial + ownership

For every Jira issue touched or referenced by the epic:

- Title length > 80 chars → WARNING "condense per `knowledge/atlassian-conventions.md` § 13".
- Title pattern mismatch vs § 13 table:
  - Spike summary not starting with `OQ:` → WARNING
  - BR summary not starting with `BR:` → WARNING
  - SD summary not starting with `SD:` → WARNING
  - Task summary starting with informal non-English verb → WARNING "use EN imperative verb"
- **Issuetype-vs-summary-prefix mismatch**: summary starts with `OQ:` but `issuetype ∈ {Task, Story}` → WARNING "should be Spike per § 1/§ 3". Conversely: `issuetype=Spike` but summary missing `OQ:` prefix → WARNING.
- Story (User Story custom field):
  - empty on issuetype=Story → CRITICAL "REQ Story field required, see `knowledge/jira-field-rules.md`"
  - > 240 chars → WARNING "approaching 255 hard cap, condense"
  - > 255 chars → CRITICAL "exceeds Atlassian hard cap, will truncate or reject"
- Spike with status=Done and resolution=null → CRITICAL "set resolution Done or Won't Do per `knowledge/atlassian-conventions.md` § 3".
- **Ownership filter**: every issue touched in any push must have a parent epic that appears in CLAUDE.md § 5 active epics table. Issues whose parent is outside scope → CRITICAL "out-of-scope edit, rollback before push". This check fires only in `--strict` mode or when invoked as a pre-push gate.

### Phase 9 — Merge-over-create (anti-duplication)

Detect candidates for issue fusion (read-only — flag for human decision, never auto-merge):

- 2+ Spike (`issuetype=Spike`) open under the same epic with ≥ 50% Jaccard token overlap on `summary` (stopwords removed) → INFO "fusion candidate".
- 2+ Story with the same High-Level-Requirements field (REQ ID) → WARNING "candidate consolidation".
- Task with non-English summary whose tokenized form matches an existing Story summary under the same epic → INFO "candidate comment-on-Story".
- OQ in `epics/{epic}/open-questions.md` with status `Open` and no `Jira Spike` link, not flagged as `(design-phase)` → WARNING "missing Spike — create or close locally".
- Spike `summary` text appearing verbatim or near-verbatim as a paragraph inside a Confluence page body (not inside a table cell) → CRITICAL "cross-system duplication, narrative belongs in Spike Description only".

## Checks performed

### Coverage Analysis (local + Atlassian)
- **Requirements → Stories**: Does every REQ have at least one linked Jira story?
- **Stories → Requirements**: Does every Jira story (sub-issue of epic) trace back to a local REQ?
- **OQ → Spikes**: Does every OQ have a linked Jira spike?
- **Acceptance criteria coverage**: Are all REQ ACs covered by story ACs?

### Atlassian Sync Checks
- **Decision sync**: All DEC in `decisions-log.md` present in the Confluence Decisions table (and vice versa). Flag missing DEC, extra DEC, mismatched dates/text.
- **OQ sync**: All Q-IDs in `open-questions.md` present in Confluence OQ table with matching status.
- **REQ sync**: All REQ in local table present in Confluence REQ table.
- **Confluence version drift**: Compare local `epic-context.md` "last updated" against Confluence current version.
- **Confluence changelog gaps**: Detect missing changelog entries (e.g. v15 → v17 with no v16) — indicates an undocumented push.
- **External comments**: Flag any Confluence/Jira comments **created after the last local changelog entry**. Stakeholder input may need triage.
- **Spike existence**: Verify Jira spikes referenced in `open-questions.md` exist (no dangling links). Detect orphan Jira spikes.

### Linkage Checks (local)
- **Orphan decisions**: DEC not referenced by any REQ, OQ, or analysis file
- **Orphan questions**: OQ not linked to a REQ or DEC
- **Dangling references**: Story references to non-existent REQ IDs
- **Missing epic context fields**: Empty fields in epic-context.md
- **Cross-epic references**: References to other epics' files — verify the referenced files exist

### Staleness Checks
- **Stale OQs**: Questions with due date past or `STALE` marker present for more than 30 days
- **Overdue ACTs**: Action items past their due date
- **Outdated epic context**: `last updated` field more than 14 days old
- **Outdated story-index**: Story index not refreshed since the most recent batch of REQ/OQ additions
- **Stale Jira issues**: Sub-issues with no update for >60 days while still in active status

### Terminology Check
- **Non-glossary terms**: Key business terms in REQ/DEC/OQ not in `knowledge/glossary.md`
- **Inconsistent naming**: Same concept referred to by different names within the epic

### Completeness Check
- **Missing sections**: REQ without acceptance criteria, scenarios, or stakeholders
- **Empty files**: Epic files that exist but have no content beyond template
- **Pending Jira sync markers**: REQ/OQ marked as needing Jira creation but not yet created (e.g. `(new spike TBD)`)

## Output format

```markdown
# Consistency Check: [Epic Name]

**Date**: [today]
**Epic**: [epic-id] ([Jira key])
**Confluence**: [URL] (current version vN)
**Mode**: full (local + Atlassian) | local-only

## Summary

| Severity | Count |
|---|---|
| Critical | [N] |
| Warning | [N] |
| Info | [N] |

## Critical Issues ([N])

| # | Check | Issue | Location | Recommendation |
|---|---|---|---|---|

## Warnings ([N])

| # | Check | Issue | Location | Recommendation |
|---|---|---|---|---|

## Info ([N])

| # | Check | Observation | Location |
|---|---|---|---|

## Atlassian Sync Diff

### Confluence — page [page-id] (v[N])

**Last updated**: [date]
**Comments since last local changelog entry**: [count]

| Element | Local | Confluence | Action |
|---|---|---|---|
| DEC count | [N] | [N] | [aligned / +X to add / -X to remove] |
| OQ count | [N] | [N] | [aligned / +X to add / -X to remove] |
| REQ count | [N] | [N] | [aligned / +X to add / -X to remove] |
| Changelog gaps | [list any version gaps detected] | | |

### Jira — epic [EPIC-KEY]

**Last updated**: [date]
**New comments since last local sync**: [count]

| Sub-issue type | Local count | Jira count | Action |
|---|---|---|---|
| Story (REQ) | [N] | [N] | [aligned / +X to create] |
| Spike (OQ) | [N] | [N] | [aligned / +X to create / X orphan] |

### External comments not yet triaged

| Source | Author | Date | Topic | Recommendation |
|---|---|---|---|---|

### In-content Jira↔Confluence drift

| Jira key | Confluence status cell | Jira status / resolution | Parent epic | Drift | Action |
|---|---|---|---|---|---|

Drift values: `Aligned` / `Confluence stale (Jira closed)` / `Jira stale (Confluence closed)` / `Title mismatch` / `Out-of-scope parent`.

### Slim profile compliance

| Table | Profile | Columns OK | Anti-duplication OK | Notes |
|---|---|---|---|---|
| Requirements | slim / canonical | ✓ / ✗ | ✓ / ✗ | [list problematic rows] |
| Open Questions | slim / canonical | ✓ / ✗ | ✓ / ✗ | [list rows > 100 chars or with forbidden columns] |
| Decisions | slim / canonical | ✓ / ✗ | ✓ / ✗ | [list rows where Rationale > 25 words] |

### Structural caps

| Metric | Measured | Cap | Status |
|---|---|---|---|
| Body storage size | [N KB] | 45 KB (slim) / 80 KB (canonical) | ✓ / ⚠ / ✗ |
| `ac:layout-section` count | [N] | exactly 3 (fixed-width + two_equal + single) | ✓ / ⚠ |
| Body/header sections breakout `wide` | [N/2] | both | ✓ / ⚠ |
| ToC in title band (not after Objective) | yes / no | yes | ✓ / ⚠ |
| `status` macros total | [N] | ≤ 25 (slim) / ≤ 50 (canonical) | ✓ / ⚠ |
| `status` macros per row (max row) | [N] | ≤ 1 | ✓ / ⚠ |
| Hardcoded dates | [N] | ≤ 10 | ✓ / ⚠ |
| Manual Changelog section | present / absent | absent | ✓ / ✗ |
| H1 present | yes / no | yes | ✓ / ⚠ |
| Table profile | slim / canonical / **mixed** | not mixed | ✓ / ✗ |
| H2 emoji prefixes | [N present / total] | all | ✓ / ⚠ / ✗ |
| Canonical H2 sections present | [N / total] | all | ✓ / ⚠ |

### Editorial findings

| Check | Count | Examples (max 3) |
|---|---|---|
| Sentences > 20 words | [N] | [snippet 1] · [snippet 2] · [snippet 3] |
| Paragraphs > 4 sentences | [N] | [section heading 1] · [section heading 2] |
| Bullet nesting > 2 levels | [N] | [path] |
| Acronyms missing first-use expansion | [N] | [acronym 1] · [acronym 2] |
| EN/foreign syntactic mix | [N] | [snippet 1] |

### Jira-side findings

| Issue | Type | Issue | Recommendation |
|---|---|---|---|
| Title > 80 chars | [key] | [length] | condense |
| Title pattern mismatch | [key] | [actual prefix] | expected `OQ:` / `BR:` / `SD:` |
| Story field empty | [key] | — | populate per § 14 |
| Story field > 240 chars | [key] | [length] | condense before 255 cap |
| Spike Done with resolution null | [key] | — | set Done or Won't Do |

### Merge-over-create candidates

| Type | Issue keys | Overlap % | Action |
|---|---|---|---|
| Spike fusion candidate | [KEY1] + [KEY2] | [%] | review for MERGE / KEEP SEPARATE / WONT DO |
| Story duplicate REQ | [KEY1] + [KEY2] (REQ-XXX-NNN) | — | consolidate one Story per REQ |
| Task → comment on Story | Task [KEY] vs Story [KEY] | [%] | convert Task to comment on Story |
| OQ without Spike | Q-XXX-NNN | — | create Spike or close OQ locally |
| Cross-system duplication | Spike [KEY] ↔ Confluence page [page-id] | — | remove narrative from Confluence, keep in Spike Description |

## Coverage Summary

| Metric | Value |
|---|---|
| Total REQ | [N] |
| REQ with Jira story | [N] |
| REQ coverage | [N%] |
| Total OQ (active) | [N] |
| OQ with Jira spike | [N] |
| OQ coverage | [N%] |
| Stories without REQ link (orphans) | [N] |
| Spikes without OQ link (orphans) | [N] |

## Push Readiness

| Action | Confluence | Jira |
|---|---|---|
| Add | [N elements] | [N issues] |
| Update | [N elements] | [N comments] |
| Close | [N elements] | [N issues] |

## Recommendations

1. [Specific, actionable recommendation prioritized by severity]
2. [...]
```

## Rules

- **Read-only operation**. Never modify files, Confluence pages, or Jira issues during a consistency check.
- Report only factual findings, not opinions or speculative interpretations.
- Severity definitions:
  - **Critical**: Missing traceability, broken references, REQ without ACs, **Confluence missing decisions/OQs that exist locally**, **dangling Jira spike references**, **changelog gaps**.
  - **Warning**: Stale OQs (>30 days), missing stakeholders, incomplete sections, **external Atlassian comments not yet triaged**, status mismatches.
  - **Info**: Style suggestions, glossary additions, minor improvements.
- If a file is empty or template-only: "Info: Template only, no content added yet."
- Omit empty severity sections.
- For Atlassian-side checks:
  - **Use the most recent local changelog entry's date** as the cutoff to identify "new" Atlassian comments.
  - **Never assume Atlassian state is correct over local state** — they can diverge in either direction. Present both sides and let the user decide which is authoritative.
  - When the Confluence changelog has a version gap, explicitly note it as a CRITICAL: someone pushed without updating the changelog.

## Atlassian tools used

The skill expects these MCP tools to be available:
- `mcp__atlassian__confluence_get_page` (page content + metadata)
- `mcp__atlassian__confluence_get_comments` (page comments)
- `mcp__atlassian__jira_get_issue` (issue + comments)
- `mcp__atlassian__jira_search` (sub-issue and spike discovery)

If any of these are not loaded at skill start, load them via `ToolSearch` before proceeding.

If the Atlassian MCP is not configured at all, fall back to `--local-only` mode and emit an INFO note explaining the partial check.

## Common patterns to detect

1. **Confluence changelog gaps**: A push happened that wasn't documented locally. CRITICAL.
2. **Decisions stuck in local but never published**: DEC exist locally but Confluence stops earlier. CRITICAL — publication gap.
3. **OQ proliferation without Jira spike creation**: Recent OQs flagged `(new spike TBD)` accumulate. WARNING if >5 pending.
4. **Status drift on closed OQs**: Local Q-XXX-NNN `Closed` but Confluence still shows `Open`. WARNING.
5. **External comments after last local changelog**: Stakeholders comment on Confluence/Jira; if not triaged, lost from local SSoT. WARNING.
6. **Stale Jira BR/epic with recent date hit**: An issue updated today but no comment added. Often stakeholder reading. INFO.
7. **Confluence cell says PARTIAL/OPEN but linked Jira is Done**: CRITICAL "drift reversed", surface in Phase 4 In-content cross-check.
8. **Out-of-scope edit**: an issue gets touched whose parent epic is not in CLAUDE.md § 5. CRITICAL in `--strict` mode.
9. **Slim REQ row with empty `Why on Confluence` cell**: Deletion candidate. CRITICAL in `--slim` mode.
10. **Decisions table `Rationale` cell > 25 words** (slim profile): The narrative belongs in the linked Jira issue. WARNING.

## Skill version

- v1: local-only consistency check
- v2: added mandatory Atlassian-side checks (Confluence + Jira), comment scans, version drift detection, changelog gap detection, push readiness summary
- v3: added Phase 4 In-content Jira↔Confluence cross-check, Phase 5 Slim profile compliance, Phase 6 Structural caps, Phase 7 Editorial / linguistic, Phase 8 Jira-side editorial + ownership filter, Phase 9 Merge-over-create candidate detection; new flags `--slim` and `--strict`
