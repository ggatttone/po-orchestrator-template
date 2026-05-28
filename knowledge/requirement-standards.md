# Requirement Standards — How We Write Requirements

This document defines the standard format for all requirements produced within the program.
All agents must follow these standards when creating or updating requirements.

**Companion files** (do not duplicate rules — reference them):
- `knowledge/atlassian-conventions.md` — Confluence page anatomy, table structures, Jira issue type title patterns, deprecated patterns
- `knowledge/jira-field-rules.md` — Jira custom field IDs, formats, caps, JQL patterns
- `knowledge/glossary.md` — terminology and inline-expansion rule

---

## Requirement structure

Every requirement must include the following sections:

### 1. Requirement ID
Format: `REQ-[EPIC-SHORT]-[NNN]`
Example: `REQ-WIM-001` (Warehouse Inventory Management, requirement 1)

### 2. Title
Short, descriptive title. Maximum 10 words.
Use action verbs where possible (e.g., "Track lot numbers on purchase orders").

### 3. Description
2-4 sentences explaining the business need.
- What is needed?
- Why is it needed?
- Who benefits?

### 3.5. Context
Every requirement must include a Context section with three parts:

**a) Why this requirement exists**: Brief explanation of the business driver — what problem it solves, what triggered it (workshop discussion, process gap, stakeholder request).

**b) Baseline analysis**: What the current platform (your ERP, CRM, planning tools — see `knowledge/systems-landscape.md`) already supports for this area:
- **OOTB**: Feature exists out-of-the-box, no changes needed
- **Config**: Feature exists but needs configuration (fields, workflows, rules)
- **Custom Objects**: New custom objects or extensions needed on the platform
- **Custom Logic**: Custom code, triggers, or integration logic required
- **Not Supported**: Platform does not support this; alternative approach needed
Reference `knowledge/systems-landscape.md` for system capabilities.

**c) Cross-epic dependencies**: List any related requirements in other epics that share data, processes, or depend on this requirement. Use specific REQ-IDs and epic keys (e.g., "Linked to REQ-ORD-006 in PRJ-305" or "Depends on PRJ-310 purchase agreement setup").

### 4. Scenarios / Examples
Use Given/When/Then format where appropriate:
```
Given [context / precondition]
When [action / trigger]
Then [expected result / outcome]
```

For simpler scenarios, bullet list format is acceptable:
- Scenario: [description of the situation and expected behavior]

### 5. Acceptance Criteria
Specific, testable conditions. Each criterion must be verifiable.
Format: Checkbox list.
```
- [ ] [Testable criterion]
- [ ] [Testable criterion]
```

Rules for acceptance criteria:
- Must be binary (pass/fail, yes/no)
- Must be independent (each criterion testable on its own)
- Must not contain "and" joining two separate conditions (split into two criteria)
- Must reference specific fields, values, or behaviors where applicable

### 6. Open Questions
Questions that need answers before the requirement can be fully specified.
Each question must include a *Context* note explaining why it matters and where it came from.
Format:
```
- [Q1]: [Question text] — Raised by: [name] — Date: [date]
  *Context*: [Why this question matters, what happens if unanswered, source reference]
```

### 7. Decisions Made
Decisions taken during workshops or reviews that affect this requirement.
Each decision must include a *Context* note explaining what triggered it and what alternatives were considered.
Format:
```
- [D1]: [Decision text] — Made by: [name/group] — Date: [date]
  *Context*: [What triggered this decision, alternatives considered]
```

### 8. Stakeholders / Owners
Who owns this requirement and who needs to be consulted.
Format:
```
- Lead Business Owner: [name]
- Consulted: [names]
```

---

## Writing style (applies to all requirement text, on Confluence and in Jira)

These rules are quantitative and testable by the `/consistency-check` skill.

### Language
- **Base language is English.** Sentences are written in one language. No mixed-language clauses.
- **Domain term-of-art is preserved** when translating would create ambiguity. The closed list of admitted non-English terms and the inline-expansion rule on first occurrence are in `knowledge/glossary.md` § "How to use glossary terms".

### Sentence and paragraph length
- Sentences ≤ **20 words**. If a sentence reaches 25+ words, split it.
- Paragraphs ≤ **4 sentences**. If you need more, switch to a bullet list.
- Bullet list nesting ≤ **2 levels**. If you reach a third level, the content belongs in a sub-section or a separate paragraph.

### Voice and tone
- Active voice preferred (`The system tracks lot status`, not `Lot status is tracked by the system`).
- Imperative descriptive tone (`The system tracks transit status at lot level`). No conditional hypothetical (`Could the system possibly track ...`).
- No formulaic verbosity (`the system shall provide the capability to support the ability to ...` → `The system tracks X.`).

### Tables
- Header cells use `<th>`, never bold-styled `<td>` (accessibility, see `knowledge/atlassian-conventions.md` § 12).
- Wrap every cell content in `<p>` tags (storage format rule).
- No empty cells. Use `—` explicitly when a value is unknown or not applicable.

### Headings
- H1 = page title (mirrors the Epic Jira title). Exactly one H1 per page.
- H2 are the fixed section names (see `knowledge/atlassian-conventions.md` § 8). Same order on every epic page.
- H3 are required inside Assumptions and Processes — at least 2 logical sub-sections each.

---

## Governance (per Confluence epic page)

Every epic master page declares its `Document Status` and `Owner` in the right-column metadata box. Confluence's native version history carries the last-updated timestamp and the change author — no need to duplicate on the page body. Full normative list of fields and the closed `Document Status` values are in `knowledge/atlassian-conventions.md` § 9.

---

## Rules for requirements work

> Universal rules (data integrity, terminology, facts vs interpretation): see `.claude/shared-rules.md`

### Preserve approved content
- Do not rewrite requirements that are already approved
- Add new requirements, new scenarios, or correct errors only
- When modifying, track what changed in the decisions log

### Context is mandatory
Context must be provided for every requirement (Section 3.5), decision (Section 7), and open question (Section 6):
- **Decisions**: what triggered it, what alternatives were considered
- **Open questions**: why it matters, what happens if unanswered, where it originated

### Before writing any requirement
1. **Baseline analysis**: Read `knowledge/systems-landscape.md`, classify as OOTB/Config/Custom Objects/Custom Logic/Not Supported, write to Context section
2. **Cross-epic scan**: Read other epics' `requirements-log.md` and `epic-context.md`, identify shared/overlapping/blocking items, write to Context section

---

## Example: Well-Written Requirement

```
## REQ-WIM-003: Track supplier compliance on purchase agreements

### Context
Why this requirement exists: Procurement currently has no visibility into whether
suppliers meet their purchase agreement commitments. Manual tracking in Excel is
error-prone and delayed, leading to underperformance going undetected for weeks.
**Baseline**: The ERP tracks agreed quantities but has no built-in compliance
calculation or deviation alerting. Custom logic needed for compliance % calculation
and threshold-based alerts. Dashboard requires Custom Objects.
**Dependencies**: Linked to REQ-ORD-010 (supplier performance monitoring) which
tracks service-level KPIs — compliance data from this requirement feeds into
the corrective action process defined there.

### Description
The system must track whether suppliers comply with the terms of their purchase agreements.
This enables procurement to identify underperforming suppliers and take corrective action.
Business Operations needs visibility into compliance rates per supplier per agreement.

### Scenarios
- Given a purchase agreement with Supplier X for 1000 units/week of Product A,
  When Supplier X delivers only 800 units in a given week,
  Then the system records a compliance deviation of -200 units (-20%).

- Given a purchase agreement with a minimum quality grade of A,
  When the received goods are graded as B,
  Then the system flags a quality compliance deviation.

### Acceptance Criteria
- [ ] The system calculates compliance percentage per delivery vs agreement terms
- [ ] Compliance deviations are visible on the supplier dashboard
- [ ] Users can filter compliance data by supplier, agreement, and date range
- [ ] The system generates alerts when compliance drops below a configurable threshold

### Open Questions
- [Q1]: Should compliance be calculated per delivery or per week? — Raised by: Alex Chen — Date: 2026-02-06
  *Context*: This affects the granularity of deviation tracking. Per-delivery gives
  real-time visibility but may flag normal variance; per-week smooths out fluctuations
  but delays detection. Source: workshop discussion on reporting frequency.

### Decisions
- [D1]: Compliance tracking is in scope for Development Block 2 — Made by: Program Board — Date: 2026-01-20
  *Context*: Originally proposed for DB3, but procurement team escalated the need due to
  ongoing supplier issues. Alternative was manual reporting — rejected as unsustainable.

### Stakeholders
- Lead Business Owner: [Name]
- Consulted: Procurement team, Operations team
```

---

## Scenario origin tags

Every scenario must be tagged with its origin to maintain traceability:

| Tag | Meaning |
|---|---|
| `Explicit (Transcript)` | Directly stated in a meeting transcription |
| `Explicit (Workshop notes)` | Directly stated in structured meeting notes |
| `Explicit (Confluence)` | Directly stated in a Confluence requirements page |
| `Inferred` | Implied but not literally stated — flag as interpretation |
| `Mixed` | Combination of multiple sources |

Rules:
- Prefer `Explicit` over `Inferred` — only use Inferred when the scenario is clearly implied but not literally stated
- When combining sources, use `Mixed` and list both sources
- Always quote or paraphrase the source material when using Explicit tags

---

## Mandatory scenario extraction from workshops

When processing workshop transcripts or meeting notes into requirements:
- Scenarios expressed during the session **MUST** be extracted and linked to the corresponding requirements
- Each scenario must include the origin tag (`Explicit (Transcript)`, `Explicit (Workshop notes)`, etc.)
- Quote or paraphrase the original discussion to preserve context
- Include the original language in parentheses when the translation may be ambiguous
- This practice is **MANDATORY** for every workshop follow-up — no requirement should be created from workshop material without checking transcripts and notes for related scenarios
- If no scenarios were discussed for a given requirement, add an explicit note: "No scenarios discussed in workshop"

---

## Confluence page format — Lightweight summary

Confluence pages are **navigation-only**. Full requirement details live in the linked Jira stories.

### Page structure

Canonical page anatomy: see `knowledge/atlassian-conventions.md` § 8 (H1 + ToC in title band; two-column header with Objective/Assumptions/Processes/Out of Scope on the left and Document Status/Owner metadata on the right; full-width body with Open Questions, Decisions, Requirements, Success metrics, User interaction, Resources sections).

Use the three-section `ac:layout` (`fixed-width` title band + `two_equal` header + `single` body). The two non-title sections must carry `breakout-mode="wide"` and `breakout-width="1800"`.

### Table structures

1. **Open Questions** — canonical 7-col / 8-col extended for pages on the canonical profile; slim 5-col for refactored pages. See `knowledge/atlassian-conventions.md` § 7 and § 7.1.
2. **Decisions** — 5-column canonical (`ID | Decision | REQ | Date | Rationale`); see § 7 / § 7.1. Rationale on Confluence is ≤ 1 line; the plain-language `What This Means` translation lives in the linked Jira issue, never duplicated on Confluence.
3. **Requirements** — canonical 5-col (`ID | Requirement | Importance | Jira Issue | Status`) for canonical pages; slim 5-col (`ID | Title | Pri | Jira Issue | Why on Confluence`) for refactored pages. On Confluence, this is a navigation table — full requirement detail lives in the linked Jira Story.

> Tables MUST be in the single-column body section to render at full page width. Placing tables in a `two_equal` cell squeezes them to ~50% width.

> For per-page quantitative caps (macro counts, H3 nesting, audience callout, governance metadata), see `knowledge/atlassian-conventions.md` § 8–9.

### Anti-duplication principle (Confluence ↔ Jira)

The same fact lives in **one** system. Confluence holds navigation + editorial context that Jira cannot carry; Jira holds operational detail. When in doubt about where to put content, ask: "Can a Jira field hold this verbatim?" — if yes, it goes in Jira and Confluence links to it. See `knowledge/atlassian-conventions.md` § 7.1 for the slim profile and the deletion-candidate rule for rows whose `Why on Confluence` would just say "see Jira".

### What goes WHERE

| Content | Location |
|---|---|
| Requirement summary (ID, title, importance, status) | Confluence — Requirements table |
| Consolidated open questions and decisions | Confluence — page-level tables |
| Objective, Assumptions, Processes, Out of Scope | Confluence — header sections |
| User Story ("As a...") | Jira — **Story** field (see `knowledge/jira-field-mapping.md`) |
| Acceptance Criteria | Jira — **Acceptance criteria** field |
| Context (why + baseline + deps) | Jira — **High level Requirements** field |
| Scenarios (with origins) | Jira — **Development report** field |
| Open Questions | Jira — **Description** field (appended as "Open Questions:" section). Verify availability of any "Outstanding Issues" custom field in your project before relying on it. |
| Cross-epic dependencies | Jira — **Dependencies** field |
| Roles impacted | Jira — **Stakeholders** field |
| Brief summary (2-3 sentences) | Jira — **Description** field (standard) |

For the full Jira field map (IDs, formats, caps, JQL patterns), see `knowledge/jira-field-rules.md` and your project mapping in `knowledge/jira-field-mapping.md`.
For the normative title patterns per Jira issue type, see `knowledge/atlassian-conventions.md` § 13.

### What does NOT go on Confluence
- No section-per-requirement (H2 per REQ) on Confluence — too long, unreadable at scale
- No manual Changelog section — use Confluence native version history (see `knowledge/atlassian-conventions.md` § 4)
- No full acceptance criteria or scenarios — those live in Jira
- For the complete list of deprecated patterns and the rationale, see `knowledge/atlassian-conventions.md` § 17

### Confluence storage format notes
- Use `storage` content format (HTML), not markdown — see `knowledge/atlassian-conventions.md` § 6 for the size-based push strategy
- **CRITICAL**: Add `ac:breakout-width="1800"` to the `ac:layout-section` tag for full-width rendering
- Use `data-layout="full-width"` on all tables for proper width
- Wrap all cell content in `<p>` tags
- Use HTML entities (`&amp;`, `&quot;`, `&#x2192;`)

---

## Open Questions format (epic-level)

The open questions file per epic (`epics/{epic}/open-questions.md`) uses an 8-column format:

```
| Q-ID | Question | Business Impact | Owner | Due | Jira Spike | Status | Decision/Note |
```

### Business Impact column

Every open question must include a Business Impact statement answering: *"If this stays unanswered, what breaks or gets delayed for which team?"*

Good Business Impact statements:
- Name the affected team or role
- Describe the concrete consequence (delays, disputes, incorrect data, blocked design)
- Are 1-2 sentences maximum

Example:
- Question: `What fixed cycle-count cadence applies per site?`
- Business Impact: `Without agreed count frequency, balance discrepancies with pool organisations go undetected until quarterly reconciliation — causing disputes and delayed settlements.`

### Plain language rule for questions

Every open question must start with **business context before the technical question**. Use this pattern:

> "Currently, [who] does [what manually / with difficulty]. We need to decide [X] because [business consequence]."

Do NOT write questions from a system perspective. Write from the stakeholder's perspective — what problem does this question solve for real people?

- Bad: `At what product hierarchy level are stocks recorded in source systems?`
- Good: `We forecast demand at different levels depending on the product. Our systems track stock at different levels, which creates gaps in planning accuracy. At what level should we align?`

### Jira Description convention for Business Impact

When creating or updating Jira spikes/stories, add a `## Business Impact` section at the top of the Description field:

```
## Business Impact
If this stays unanswered, [who] cannot [do what], causing [consequence].

## Question Details
[existing spike description]
```

This requires no custom fields — it works immediately in the standard Description field.

---

## Decisions Log format (epic-level)

The decisions log per epic (`epics/{epic}/decisions-log.md`) uses this format:

```
| ID | Date | Decision | What This Means | Rationale | Made By |
```

### What This Means column

Every decision must include a plain-language translation answering: *"What does this mean for daily work?"*

This column translates technical decisions into consequences that non-technical stakeholders can understand.

Example:
- Decision: `Draft orders do NOT reserve dock capacity. Only Reserved status locks dock slots.`
- What This Means: `Sales can freely create draft orders without blocking loading times for other customers. No phantom dock overbooking.`

### Jira Description convention for What This Means

When logging decisions on Jira spikes/stories, add a `## What This Means` section:

```
## What This Means
[plain-language consequence for daily work]

## Decision Details
[existing decision description]
```

---

## Requirements Log format (local)

The requirements log per epic (`epics/{epic}/requirements-log.md`) uses this format:

### Summary table

| ID | Title | Importance | Jira Issue | Status |
|---|---|---|---|---|
| REQ-WIM-001 | Real-time stock sync | HIGH | PRJ-201 | Draft |

### Detail sections

```
## REQ-WIM-001: [Title]

**Source**: [document/page reference]
**Importance**: HIGH / MEDIUM
**Platform Fit**: [assessment]

### Context
[Why it exists]
**Baseline**: [platform assessment]
**Dependencies**: [cross-epic links]

### User Story
As a [role], I want [capability], so that [benefit].

### Description
[2-4 sentences]

### Scenarios
- **Scenario 1**: [concrete example from documentation]
  - *Origin*: [tag] — [quote or reference to source]
- **Scenario 2**: ...

### Acceptance Criteria
- [testable criterion]

### Open Questions
- [question]
  *Context*: [why it matters]
```
