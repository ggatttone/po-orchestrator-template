# Atlassian Conventions — Confluence + Jira

Canonical conventions for working with your Atlassian instance (`YOUR-INSTANCE.atlassian.net`).
All agents must follow these conventions. Other files reference this one; do not duplicate.

<!-- CUSTOMIZE: Update the date when you adopt these conventions for your project. -->

Date adopted: YYYY-MM-DD.

---

## 1. Open Question (OQ) classification — standalone Jira Spike vs. design-phase OQ

When triaging a new Open Question or creating one, decide whether it deserves a standalone Jira Spike or stays as a design-phase OQ inside a REQ story.

**Create a standalone Jira Spike** when the OQ requires active investigation:
- Business confirmation needed (e.g., stakeholder decision on policy)
- Specialist input (legal, finance, customs, security)
- Workshop or session topic to schedule
- Cross-epic decision affecting multiple teams
- Operational change (process redesign, role redefinition)
- New scope discovery (unknown unknown surfaced)

**Do NOT create a standalone Spike** when the OQ is design-phase:
- [Your ERP] fit / implementation design
- Storage model choice
- Scalability concern internal to a REQ
- UI/UX detail inside a known scope

Design-phase OQs live inside the REQ Story (Description or comment) and are resolved during the design phase. If a Spike has already been created for what turns out to be a design-phase OQ, close it with resolution `Won't Do` and a comment pointing to the linked REQ.

## 2. OQ ↔ Spike linkage (1:N pattern)

One Jira Spike can cover multiple `Q-{EPIC}-NNN` (1:N linkage). Closure of a Spike requires that **all** linked questions are resolvable. Default to 1:1 for new Spikes; consolidate to 1:N when topics overlap (see merge-over-create principle below).

## 3. Spike workflow

<!-- CUSTOMIZE: Confirm transition IDs against your project's workflow. -->

Status transitions:
- `Backlog` → transition id (e.g. **11 (Activate)**) → `In Progress`
- `In Progress` → transition id (e.g. **61 (Finish)**) → `Done`

Resolution semantics on close:
- `Done` — question substantively answered; decision recorded in linked REQ or Spike comment
- `Won't Do` — not pursued autonomously (e.g., scope routed to REQ design phase)
- **`null` is an anomaly** — every closed Spike must have an explicit resolution

## 4. Confluence Changelog convention

**No manual Changelog sections on Confluence pages.** Use Confluence's native version history. Each push must set a descriptive `version.message` (e.g., `"v23: refactor to 2-section layout; deduplicate Requirements table"`).

The local file `docs/changelog.md` is separate and remains the project log (chronological cross-epic events).

## 5. Inline link annotations for closed Jira Spikes

When a closed Spike is referenced inline on a Confluence page (e.g., inside an OQ row), annotate the link with its closure state:

```html
<a>PRJ-1758</a> <em>(closed Done YYYY-MM-DD)</em>
<a>PRJ-1762</a> <em>(closed Won't Do YYYY-MM-DD — routed to REQ-EPIC-007)</em>
```

This makes the closure visible without forcing the reader to open Jira. Mandatory for OQ tables and Resources sections.

## 6. Confluence storage format

**Storage format only** for page updates. Never markdown roundtrip — Confluence's markdown converter drops macros (`details`, `status`, `expand`, `jira`, `excerpt-include`) and breaks layout sections.

Mechanics:
- Pages < 30 KB: edit via MCP `confluence_update_page` if available, else REST API directly
- Pages 30–60 KB: REST API + targeted `str.replace()` on the storage HTML
- Pages > 60 KB: delegate to subagent + Python REST API + `str.replace()`

Backup every page before modification: `.rollback/confluence-backup/{pageId}-v{N}-backup.html`.

## 7. Confluence table structures (canonical)

| Table | Columns | Order |
|---|---|---|
| **Requirements** | 5 | `ID \| Requirement \| Importance \| Jira Issue \| Status` |
| **Open Questions (standard)** | 7 | `Q-ID \| Question \| REQ \| Responsible \| Jira Spike \| Answer \| Status` |
| **Open Questions (extended)** | 8 | as standard + `Business Impact` column |
| **Decisions** | 5 | `ID \| Decision \| REQ \| Date \| Rationale` |

OQ status values use Confluence status macro with TitleCase `colour` + UPPERCASE `title`:

| Logical state | Macro | When |
|---|---|---|
| Open / unanswered | `Blue/OPEN` | Spike Backlog or In Progress, no answer yet |
| Partial answer | `Yellow/PARTIAL` | Spike open, some sub-questions answered |
| Direction set | `Yellow/DIRECTION SET` | Spike open, direction decided, mechanism deferred to SD |
| Answered (live) | `Green/ANSWERED` | Spike open, substantive answer captured (transient — typically leads to closure) |
| Stale / overdue | `Red/OPEN` or `Red/OVERDUE` | Spike open beyond threshold, escalation needed |
| **Closed** | `Grey/CLOSED` | Spike `status=Done` regardless of resolution. Inline `<em>(closed Done\|Won't Do YYYY-MM-DD — reason)</em>` annotation carries the qualitative detail |

Rule: when a Spike transitions to `Done` (any resolution), the linked OQ row normalises its status macro to `Grey/CLOSED`. The annotation describes HOW it closed (Done = answered or scope; Won't Do = design-phase or transferred). Single uniform "closed" colour reduces table scanning cost; readers spot live items at a glance.

The local epic file `epics/{epic}/open-questions.md` uses an **8-column local format** (different from Confluence): `Q-ID | Question | Business Impact | Owner | Due | Jira Spike | Status | Decision/Note`. Local-only — never push as-is to Confluence.

## 7.1 Slim profile (anti-duplication principle) — applies to refactored pages

**Principle**: Confluence = navigation + editorial context. Jira = detail (Story field, AC, Description, Scenarios, Resolution). The same fact must not be stored in both — when refactoring a page, **relocate**, do not eliminate. Anything removed from Confluence already lives in Jira; anything added to Confluence is exclusive editorial value (cross-REQ dependency, design trade-off, audience note) that Jira cannot carry.

Per-page decision: a single page is either fully on slim or fully on canonical — never mixed within the same page.

### Slim Requirements table — 5 columns

| # | Column | Width | Content |
|---|---|---|---|
| 1 | **ID** | 130px | `REQ-{EPIC}-NNN` |
| 2 | **Title** | 520px | Short title (≤ 12 words) |
| 3 | **Pri** | 80px | `H` / `M` / `L` (drops detailed Importance — full priority lives on Jira issue) |
| 4 | **Jira Issue** | 130px | Link to Story |
| 5 | **Why on Confluence** | 740px | 1-line editorial note: cross-REQ dependency, design trade-off, audience-specific impact. **Cannot just restate the title** — must add value not present in Jira. |

What is dropped (and where it lives in Jira):
- `Status` column → `status` field of the linked Jira Story
- `Importance` (detailed wording like `Should have`) → MoSCoW custom field on the Jira Story (see `knowledge/jira-field-mapping.md`)
- `Description` → standard `description` field on the Jira Story
- Long requirement text → Story and High-Level-Requirements custom fields (see `knowledge/jira-field-mapping.md`)

### Slim Open Questions table — 5 columns

| # | Column | Width | Content |
|---|---|---|---|
| 1 | **Q-ID** | 100px | `Q-{EPIC}-NNN` |
| 2 | **Question** | 480px | ≤ 100 chars. Full question text lives in the Jira Spike Description (`## Question Details`) |
| 3 | **Responsible** | 200px | Person name(s) |
| 4 | **Jira Spike** | 150px | Link to Spike |
| 5 | **Status** | 130px | Status macro per § 7 table (`Blue/OPEN`, `Yellow/PARTIAL`, `Yellow/DIRECTION SET`, `Green/ANSWERED`, `Grey/CLOSED`) |

What is dropped (and where it lives in Jira):
- `Business Impact` narrative → `## Business Impact` section in Jira Spike Description
- `Answer` long-form → Jira Spike comment thread + Resolution comment on close
- `Direction set` notes → Jira Spike comment with appropriate label
- `Date` column → Jira issue update timestamp is the source of truth; closure date is already in the inline annotation `(closed Done YYYY-MM-DD)` on the Spike link when applicable

Confluence retains the short question (so a reader can scan the table) and the link to Jira; the body of the discussion belongs in the Spike.

### Slim Decisions table — 5 columns (unchanged from canonical)

`ID | Decision | REQ | Date | Rationale`. The `Rationale` cell is **≤ 1 line** (≤ 25 words). Anything longer — including the `What This Means` plain-language translation — lives in the linked Jira Story / Spike Description, not duplicated on Confluence.

### Slim profile enforcement

- The `Why on Confluence` column cannot be empty. If you cannot articulate why a REQ row deserves a row on Confluence beyond "it exists in Jira", do not include the row — the slim table is curated, not exhaustive. The Jira backlog already lists all stories.
- A row whose `Why on Confluence` value would be `"see Jira"` or `"linked story"` is a deletion candidate.
- For pages refactored to slim, the consistency-check skill (v3) flags as a Critical any narrative content on Confluence that is also present verbatim in the linked Jira issue (anti-duplication check).

## 8. Confluence page anatomy (epic master pages)

Three layout sections:

**Section 1 — `ac:layout-section ac:type="fixed-width" ac:breakout-mode="default"` (title band)**
- Single cell: the H1 (page title, format `Title &mdash; EPIC-KEY`, e.g. `Order Processing &mdash; PRJ-101`) followed immediately by the `toc` macro — nothing else. This places the table of contents at the very top of the page.

**Section 2 — `ac:layout-section ac:type="two_equal" ac:breakout-mode="wide" ac:breakout-width="1800"` (header)**
- Left column: Objective, Assumptions, Processes, Out of Scope
- Right column: metadata box (`Document Status | Owner`) — Epic link + supporting roles (Business Owner, Consultant) also live here

**Section 3 — `ac:layout-section ac:type="single" ac:breakout-mode="wide" ac:breakout-width="1800"` (body)**

> **Full-width mechanism:** the body section spans the full page width via `ac:breakout-mode="wide" ac:breakout-width="1800"` on the `<ac:layout-section>` tag itself. `data-layout="full-width"` and `data-table-width="1800"` are **table-level** attributes (`<table data-layout="full-width" data-table-width="1800">`), not section attributes; every data table in the body section also carries them. Both the header and body sections take the `wide` breakout.
- H2 order (fixed, non-reorderable): Open Questions → Decisions → Requirements → Success metrics → User interaction → Resources
- H3 nesting required inside Assumptions and Processes (≥ 2 sub-sections each)
- The `toc` macro lives in Section 1 (title band) at the top of the page — **not** after Objective

**H2 emoji prefix (mandatory, exact glyphs):**

| Section | Emoji prefix |
|---|---|
| Objective | 🎯 |
| Assumptions | 🤔 |
| Processes | ⚙️ |
| Out of Scope | ⚠️ |
| Open Questions | ❓ |
| Decisions | ↔️ |
| Requirements | 📝 |
| Success metrics | 📊 |
| User interaction, design & processes | 🎨 |
| ERP Baseline (optional) | 📋 |
| Resources | 🔖 |

Use the literal UTF-8 glyph in the source you send. **Note:** Confluence storage *serialises* `U+2194 ↔` to the HTML entity `&harr;` on save — this round-trip is unavoidable platform behaviour and is **not** itself the anti-pattern. The real anti-pattern is the Decisions heading rendering as a plain monochrome arrow because the `U+FE0F` variation selector is missing: the stored form must be `&harr;` immediately followed by `U+FE0F` (renders as the ↔️ emoji). Send `↔️` (U+2194 U+FE0F) — Confluence stores `&harr;` + U+FE0F, which renders correctly.

Quantitative caps per page:
- `ac:layout-section` count: exactly 3 (title band `fixed-width` + header `two_equal` + body `single`)
- `ac:structured-macro[name=status]`: ≤ 1 per table row (only in Status columns)
- `ac:structured-macro[name=jira]`: only inside Jira Issue / Jira Spike columns, never inline in narrative
- `ac:structured-macro[name=details]`: exactly 1 (header metadata)
- `ac:structured-macro[name=expand]`: only for historical context or audience-specific sections
- `ac:structured-macro[name=toc]`: exactly 1
- Hardcoded absolute dates (`YYYY-MM-DD`): ≤ 10 **in narrative text** (prefer links to Jira sprint / calendar event). The closure annotation dates inside `(closed Done YYYY-MM-DD)` Jira-Spike inline annotations (see § 5) and the `Date` column of Decisions table are exempt — they carry traceability value and have no upper cap.

## 9. Confluence governance — living document

Every epic page must declare:
- `Document Status`: one of `Living draft` / `Sprint-ready` / `Frozen — handed off to dev` / `Archived`
- `Owner`: PO or designated maintainer

Pages marked `Living draft` and `Sprint-ready` require monthly review (handled out-of-band, not on the page itself). `Frozen` pages are snapshots — modifications happen in a new page revision, not in-place edits.

Confluence native version history carries the `Last updated` timestamp and the change author. No need to duplicate on the page body. Audience targeting and review cadence live in the team's operating model, not on each page.

## 10. Confluence Space-level Glossary

Each Confluence space maintains a `Glossary` page at the space root.
Format: 4-column table (`Term | Expansion | Definition | Pages using`).

Each acronym or domain term-of-art used in epic pages must have an entry in the space Glossary. First occurrence on a page expands inline (e.g. `PO (Purchase Order)`); subsequent occurrences use the term alone. For pages with > 50 acronym occurrences, embed the Glossary via `excerpt-include` instead of duplicating the expansion inline.

## 11. Confluence keyword labels (space search)

Every epic page must carry semantic labels: e.g. `#wms`, `#mrp`, `#rtv`, `#po-indirect`. Used by space-level search and dashboard filtering.

## 12. Confluence accessibility

- Tables: header cells with `<th>`, never bold-styled `<td>`
- Images / diagrams: alt text mandatory; if a diagram conveys process logic, also provide a tabular equivalent
- Lucidchart / view-file embeds: ensure underlying file is also indexed in space search

## 13. Jira issue type — title patterns (normative)

| Issue type | Title pattern | Example | Anti-example |
|---|---|---|---|
| Epic | `[Domain] - [Function (variant)]` | `WMS Integration - Stock levels in the day` | `WMS stuff` |
| Story | descriptive action phrase, EN, ≤ 80 char | `Track lot numbers on purchase orders` | very long mixed-language phrasing |
| Spike (OQ-type) | `OQ: [Topic EN]`, ≤ 70 char | `OQ: External inspection handling for import` | `Spike about external check` |
| Spike (operational) | `[Event/Activity descriptive]`, ≤ 70 char | `Portal Demo Prep`, `POC Execution` | use only for workshop prep / demos / one-off operational threads, never for Open Questions |
| Business Refinement | `BR: [Function]` | `BR: Fulfillment` | `Refinement fulfillment` |
| Solution Design | `SD: [Function]` | `SD: Order Processing` | `Design doc orders` |
| Technical Story | descriptive technical phrase, no prefix | `Summarize VAT per tax location on invoice` | `Tech: vat thing` |
| Task | `[Verb]: [Object EN]`, ≤ 80 char | `Document customs clearance flow in Confluence` | informal phrasing in non-English |
| Bug | `[Module]: [observed behaviour]`, ≤ 80 char | `Stock view: transit lots appear as available` | `bug stock` |
| Sub-task | inherits parent's language and style | — | — |

Language rule: English base. Domain term-of-art preserved in original language when no English equivalent is unambiguous (document these exceptions in `knowledge/glossary.md`).

## 14. Jira Story field requirement

The `User Story` custom field (see `knowledge/jira-field-mapping.md`) is **required** on issue type `Story`, optional on `Technical Story`, **not used** on `Spike`, `Business Refinement`, `Solution Design`, `Task`, `Bug`, `Sub-task`.

Format on Story type: `As a [role ≤ 30 char], I want [capability ≤ 120 char], so that [benefit ≤ 80 char].`

Hard cap: 255 characters (Jira-enforced). Operational cap: **≤ 240 characters** (margin for safe save). See `knowledge/jira-field-rules.md` for the full field reference.

## 15. Merge-over-create — duplicate-avoidance principle

Before creating a new Spike, Story, or Task, JQL-search for existing issues on the same topic. If found:
- Same scope → add a comment, extend AC, update description on the existing issue. Do not create a duplicate.
- Adjacent scope → consider the 1:N Spike pattern (one Spike, multiple linked Q-IDs) instead of two parallel Spikes.
- Different scope but same area → link via `relates to` (link type id `10003` in standard Jira instances).

Audit periodically: the count of open Spikes should trend down over time, not up.

## 16. Canonical status names (case-sensitive)

<!-- CUSTOMIZE: Verify these statuses match your project's workflow exactly. Case matters in JQL. -->

Workflow status values as commonly used. **Case matters** when filtering via JQL or referencing in documentation.

| Status | Category | Case |
|---|---|---|
| `TO BE REFINED` | To Do | UPPERCASE — used for early-stage epics |
| `Business Refined` | To Do | TitleCase — refinement complete, awaiting design |
| `Sprint ready` | To Do | TitleCase with lowercase `r` in `ready` |
| `Backlog` | To Do | TitleCase |
| `Parked` | To Do | TitleCase — work paused |
| `In Progress` | In Progress | TitleCase with space |
| `TEST` | In Progress | UPPERCASE |
| `Done` | Done | TitleCase |

Common pitfall: writing `Sprint Ready` (capital `R`) in a JQL filter returns zero results. Always quote the status verbatim from the live issue.

## 17. Deprecated patterns

| Pattern | Reason | Replacement |
|---|---|---|
| 11-column Requirements table on Confluence | Information overload; most columns duplicated Jira | 5-column slim table (see § 7) |
| Manual Changelog sections on Confluence | Duplicates Confluence version history; falls out of sync | `version.message` on each push (see § 4) |
| `docs/iteration-log.md` | Replaced by `docs/changelog.md` | See `docs/changelog.md` |

---

## References

- Jira field rules (custom fields, caps, formats): `knowledge/jira-field-rules.md`
- Jira field mapping (your IDs): `knowledge/jira-field-mapping.md`
- Requirement structure: `knowledge/requirement-standards.md`
- Glossary (terms, acronyms): `knowledge/glossary.md`
