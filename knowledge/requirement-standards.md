# Requirement Standards — How We Write Requirements

This document defines the standard format for all requirements produced within the program.
All agents must follow these standards when creating or updating requirements.

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

**b) Baseline analysis**: What the current platform already supports for this area:
- **OOTB**: Feature exists out-of-the-box, no changes needed
- **Config**: Feature exists but needs configuration (fields, workflows, rules)
- **Custom Objects**: New custom objects or extensions needed on the platform
- **Custom Logic**: Custom code, triggers, or integration logic required
- **Not Supported**: Platform does not support this; alternative approach needed
Reference `knowledge/systems-landscape.md` for system capabilities.

**c) Cross-epic dependencies**: List any related requirements in other epics that share data, processes, or depend on this requirement. Use specific REQ-IDs and epic keys.

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
  When Supplier X delivers only 800 kg in a given week,
  Then the system records a compliance deviation of -200 kg (-20%).

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

### Page structure (two-section layout)

Use `ac:layout` with TWO `ac:layout-section` elements. Both must have `breakout-mode="wide"` and `breakout-width="1800"`.

**Section 1** — `type="two_equal"` (two-column header):

| Left column | Right column |
|---|---|
| H2: Objective | `ac:structured-macro` name="details" with metadata table |
| H2: Assumptions | (Target release, Epic, Document status, Document owner, PO, BO, Consultant) |
| H2: Processes | |
| H2: Out of Scope | |

**Section 2** — `type="single"` (full-width tables and remaining content):

1. H2: Open Questions — 5-column table (Question | REQ | Responsible | Answer | Date)
2. H2: Decisions — 4-column table (Decision | REQ | Date | Rationale)
3. H2: Requirements — **5-column summary table only** (ID | Requirement | Importance | Jira Issue | Status)
4. H2: Success metrics
5. H2: User interaction, design & processes
6. H2: Resources
7. (Optional) H2: Platform Baseline

> Tables MUST be in the single-column section to render at full page width. Placing tables in a `two_equal` cell squeezes them to ~50% width.

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
| Open Questions | Jira — **Description** field (appended as "Open Questions:" section) |
| Cross-epic dependencies | Jira — **Dependencies** field |
| Roles impacted | Jira — **Stakeholders** field |
| Brief summary (2-3 sentences) | Jira — **Description** field (standard) |

### What does NOT go on Confluence
- No section-per-requirement (H2 per REQ) on Confluence — too long, unreadable at scale
- No Change Log section — use Confluence version comments instead
- No full acceptance criteria or scenarios — those live in Jira

### Confluence storage format notes
- Use `storage` content format (HTML), not markdown
- **CRITICAL**: Add `ac:breakout-width="1800"` to the `ac:layout-section` tag for full-width rendering
- Use `data-layout="full-width"` on all tables for proper width
- Wrap all cell content in `<p>` tags
- Use HTML entities (`&amp;`, `&quot;`, `&#x2192;`)

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
