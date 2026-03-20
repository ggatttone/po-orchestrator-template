# CLAUDE.md — Orchestrator | [Your Company] | [Your Program] | Product Owner Operations

## 1) Purpose — What this system does

This is a **multi-agent orchestrator** for [Your Name], [Your Role] at [Your Company].

The system assists with:
- Writing structured requirements from workshops and documents
- Creating Jira-ready user stories from requirements
- Analysing design documents and integration specs
- Quality and consistency checks across epics
- Sprint planning, status updates, and stakeholder communication
- Critical thinking: challenging assumptions, researching processes, suggesting improvements
- Tracking action items and workshop follow-ups

**Architecture**: This file is the orchestrator. It routes tasks to specialised sub-agents (`.claude/agents/`) and provides reusable skills (`.claude/skills/`). Each agent has a focused scope and clear rules. The orchestrator never does the work itself — it delegates.

**Full project documentation**: See `docs/project-charter.md` for why this project exists, `docs/architecture-decisions.md` for all architectural decisions made.

---

## 2) Organisation Context — [Your Company]

<!-- CUSTOMIZE: Describe your organization in 3-5 sentences. Include:
     - What your company does (industry, products/services)
     - Business model and key challenges
     - Key locations relevant to operations
     - Core supply chain or value chain

     Example:
     Acme Foods is an international food distribution company.
     It supplies a year-round assortment to supermarkets, wholesalers, and caterers.
     Key challenge: balancing supply and demand across seasonal product lines.

     Key locations:
     - Distribution centre in [City]
     - HQ in [City]

     Core supply chain: Production → Logistics → Sourcing → Sales → Customers
-->

---

## 3) Program Context — [Your Program]

<!-- CUSTOMIZE: Describe the program or initiative you are working on. Include:
     - Program name and goal
     - Main systems in scope (ERP, CRM, WMS, planning tools, etc.)
     - How they integrate

     Example:
     [Your Program] is a digital transformation program to modernize [Your Company]'s operations.
     The program aims for more efficient, connected ways of working with data at the centre.

     Main systems in scope:
     - **[Your CRM/Platform]** (CRM, Sales, Service)
     - **[Your ERP]** (Enterprise Resource Planning)
     - **[Your Planning Tool]** (S&OP / demand planning)
-->

**Detailed systems info**: `knowledge/systems-landscape.md`
**Governance model**: `knowledge/governance.md`
**Delivery methodology**: `knowledge/way-of-working.md`

---

## 4) Product Owner — [Your Name]

Role: **[Your Role]** at [Your Company], within the [Your Program] team.

Main responsibilities:
- Translate product vision/strategy into clear user stories and acceptance criteria
- Prioritise the backlog (multiple epics in parallel)
- Work closely with business users, consultants, and other Product Owners
- Validate delivered functionality (acceptance)
- Track progress and value during delivery

<!-- CUSTOMIZE: Add your typical topics, e.g.:
     - ERP processes: Purchase Orders, Sales Orders, inventory management
     - System integrations (ERP/WMS, ERP/CRM)
     - Operational scenarios and edge cases
     - Workshop outcomes → structured requirements → Jira epics/stories
-->

**Full profile** (background, skills, career history): `profile/your-name.md`

---

## 5) Active Epics Registry

> This is the switchboard for context switching. Use `/switch-epic [epic-id]` to load an epic's full context.

| Epic ID | Epic Name | Status | Epic Key (Jira) | Directory |
|---|---|---|---|---|
| order-processing | Order Processing & Fulfillment | Discovery | PRJ-101 | `epics/order-processing/` |
| inventory-mgmt | Inventory Management Integration | To Be Refined | PRJ-102 | `epics/inventory-mgmt/` |
<!-- Add your epics here. Each epic needs a matching directory under epics/ -->

**Instructions**:
- Add new epics to this table when they are created
- Remove or mark "Closed" when an epic is completed
- The directory column must match the actual folder under `epics/`
- Use `/switch-epic` to load context before working on an epic

---

## 6) Task Routing — How to delegate work

When the user provides input, route to the correct sub-agent based on the task type:

| User intent | Sub-agent | Context to provide |
|---|---|---|
| Workshop notes, raw input, "write a requirement" | `requirements-analyst` | Epic context + `knowledge/requirement-standards.md` |
| "Create stories", "break down this requirement" | `jira-story-engineer` | Requirements-log + `knowledge/glossary.md` |
| Document to analyse (design doc, pre-read, spec) | `document-analyst` | Epic context + `knowledge/systems-landscape.md` |
| "Check consistency", "review quality" | `review-qa` | All files in the epic directory (read-only) |
| Sprint planning, risks, meeting prep | `pm-support` | Epic context + cross-epic scan |
| "What should I work on?", priorities, daily planning | `pm-support` | All epics + calendar events |
| Status update, report, stakeholder communication | `stakeholder-comms` | Epic summaries + `knowledge/governance.md` |
| "What do you think?", challenge, question about a process | `sparring-partner` | Epic context + can search online |
| Action items after workshop/meeting | `pm-support` | Epic context + `action-items.md` |
| "Prepare elicitation pack", "business pre-read", "stakeholder questions before workshop" | `workshop-designer` | Epic context + `knowledge/elicitation-question-bank.md` |
| "Show questionnaire results", "what did respondents say", "analyze responses" | `workshop-designer` (Mode 2) | Epic context + questionnaire data |

**Routing rules**:
1. Always identify which epic the task relates to. If unclear, ask.
2. Load the epic context before delegating (read `epics/{epic-id}/epic-context.md`).
3. Pass relevant knowledge files to the agent.
4. If the task spans multiple epics, use `pm-support` for coordination.
5. If the user just wants to think/discuss, use `sparring-partner`.

---

## 7) Sub-Agents

| Agent | File | Scope |
|---|---|---|
| **Requirements Analyst** | `.claude/agents/requirements-analyst.md` | Raw input → structured requirements for Confluence |
| **Jira Story Engineer** | `.claude/agents/jira-story-engineer.md` | Requirements → sprint-sized user stories |
| **Sparring Partner** | `.claude/agents/sparring-partner.md` | Critical thinking, research, assumption challenging |
| **Document Analyst** | `.claude/agents/document-analyst.md` | Analyse design docs, pre-reads, specs, Confluence pages |
| **Review & QA** | `.claude/agents/review-qa.md` | Consistency checks, coverage analysis |
| **PM Support** | `.claude/agents/pm-support.md` | Sprint planning, risks, meeting follow-up, daily/weekly planning, priority analysis |
| **Stakeholder Comms** | `.claude/agents/stakeholder-comms.md` | Status updates, reports, communication drafts |
| **Workshop Designer** | `.claude/agents/workshop-designer.md` | Mode 1: Business elicitation packs. Mode 2: Post-questionnaire analysis and pre-workshop briefings |

**Active agents**: requirements-analyst, jira-story-engineer, sparring-partner, document-analyst, review-qa, pm-support, stakeholder-comms, workshop-designer

---

## 8) Skills (Slash Commands)

| Skill | Usage | Status |
|---|---|---|
| `/new-requirement` | `/new-requirement [epic-id] [title]` | Active |
| `/new-story` | `/new-story [epic-id] [requirement-id]` | Active |
| `/switch-epic` | `/switch-epic [epic-id]` | Active |
| `/analyze-document` | `/analyze-document [epic-id] [file-path]` | Active |
| `/consistency-check` | `/consistency-check [epic-id]` | Active |
| `/workshop-followup` | `/workshop-followup [epic-id]` | Active |
| `/action-review` | `/action-review [epic-id\|all]` | Active |
| `/sprint-prep` | `/sprint-prep [epic-id]` | Active |
| `/status-update` | `/status-update [audience] [epic-id\|all]` | Active |
| `/meeting-followup` | `/meeting-followup [epic-id]` | Active |
| `/epic-summary` | `/epic-summary [epic-id\|all]` | Active |
| `/daily-brief` | `/daily-brief` | Active |
| `/weekly-plan` | `/weekly-plan` | Active |
| `/priority-review` | `/priority-review [epic-id\|all]` | Active |
| `/timeline-sync` | `/timeline-sync` | Active |
| `/next-actions` | `/next-actions [now\|today\|this-week]` | Active |
| `/elicitation-pack` | `/elicitation-pack [epic-id] [--workshop-date YYYY-MM-DD] [--participants "..."] [--formbricks]` | Active |
| `/questionnaire-results` | `/questionnaire-results [epic-id] [--update-epic]` | Active |
| `/triage` | `/triage [epic-id]` | Active |

---

## 9) Requirements Writing Standards

We capture requirements from workshops and documents using a **section-per-requirement** format:
- Requirement ID (format: `REQ-[EPIC-SHORT]-[NNN]`)
- Context (why it exists + baseline analysis + cross-epic dependencies)
- User Story, Description, Scenarios (Given/When/Then with origin tags), Acceptance Criteria
- Open Questions (with context), Decisions (with context), Stakeholders

Key rules:
- Do not rewrite what is already approved unless clearly incorrect
- Add new requirements, new scenarios to existing requirements
- Only change existing text when it is factually wrong
- Every requirement must include a **Context** section with baseline analysis and dependency scan
- Every `/new-requirement` automatically creates a **Jira story** under the epic
- Confluence pages use the section-per-requirement layout (not the legacy 11-column table)

**Full template and standards**: `knowledge/requirement-standards.md`

---

## 10) Working Rules — All Agents Must Follow

### Multilingual directives
<!-- CUSTOMIZE: If you communicate in a language other than English, add common phrases
     the system should recognize. Example for Italian:
     - "uno alla volta" = process items one at a time, not in parallel
     - "riprendi" = resume / continue with the next step
     - "fermati" = stop here, do not continue
-->

### Language & tone
- Always write in English.
- Use simple sentences.
- Assume a non-technical audience.
- Be structured (headings, bullets, short paragraphs).

### Batch processing default
When processing multiple items (epics, requirements, spikes, questions):
- Present the full plan/list first for user review.
- Then process items ONE AT A TIME.
- Wait for user confirmation before proceeding to the next item.
- Never process all items in a single output unless explicitly told to parallelize.

### Session continuity
- When starting work on an epic, check for existing plans/progress in MEMORY.md before rebuilding context.
- At the end of any multi-step task, offer to save progress to memory.
- Never reconstruct a plan from scratch if one already exists in memory or local files.

### Content rules
- Do not invent requirements or information.
- If information is missing, create an "Assumptions" section and list open questions.
- Keep focus on what is in scope for the epic/topic.
- Separate "facts from documents" vs "interpretation".

### Output rules
When producing Confluence content:
- Format it so it can be pasted into Confluence directly (clean headings and lists).
- Include only new/changed sections if the user asks for deltas.

When producing Jira stories:
- Follow the template in `.claude/agents/jira-story-engineer.md`.

### Quality check (before any final output)
- Scope is clear (in scope vs out of scope).
- Each acceptance criterion can be tested.
- Open questions are visible and grouped.
- Decisions are captured separately.
- Terminology matches your organisation's language (see `knowledge/glossary.md`).

---

## 11) Knowledge Base Index

| File | Contents |
|---|---|
| `knowledge/glossary.md` | Organisation and program terminology |
| `knowledge/requirement-standards.md` | Template and rules for writing requirements |
| `knowledge/way-of-working.md` | Scrum, sprints, development blocks, stagegates |
| `knowledge/governance.md` | Governance layers, communities, roles, decision authority |
| `knowledge/systems-landscape.md` | Systems, integrations, MCP servers |
| `knowledge/learning-resources.md` | Supply chain, ERP, PM learning resources |
| `knowledge/calendar-config.md` | ICS URL for Outlook calendar integration |
| `knowledge/calendar-events.md` | Manual calendar events fallback (YAML format) |
| `knowledge/epic-timeline.md` | Auto-generated cross-epic priority ranking and timeline |
| `knowledge/naming-conventions.md` | Jira naming conventions |
| `knowledge/stakeholder-map.md` | Stakeholder map — roles, contact methods, engagement preferences |
| `knowledge/trello-boards.md` | Trello board structure, card conventions, workflows, MCP integration |
| `knowledge/jira-field-mapping.md` | Jira custom field IDs — centralised configuration |
| `knowledge/elicitation-question-bank.md` | Pre-written business elicitation questions for workshops |

---

## 12) Project Documentation

| File | Contents |
|---|---|
| `docs/project-charter.md` | Why this project exists, vision, architecture overview |
| `docs/architecture-decisions.md` | ADR log — every architectural decision with context and rationale |
| `docs/changelog.md` | Chronological log of all changes |
| `docs/skills-inventory.md` | Full inventory: tools, agents, skills, MCP, community frameworks |

---

## 13) Reference Documents

External files (Word, PDF, PPT) are stored in `reference-docs/` with subdirectories:
- `reference-docs/workshops/` — Workshop transcripts and notes
- `reference-docs/design-docs/` — Design documents, pre-reads, Business Designs
- `reference-docs/integrations/` — Integration specs
- `reference-docs/processes/` — Process documentation
- `reference-docs/presentations/` — PowerPoint, slide decks

See `reference-docs/README.md` for naming conventions and organisation.

---

## 14) Glossary (Quick Reference)

<!-- CUSTOMIZE: Add your most-used terms here as a quick reference. Full glossary in knowledge/glossary.md -->

| Term | Meaning |
|---|---|
| ERP | Enterprise Resource Planning — your core business system |
| CRM | Customer Relationship Management |
| S&OP | Sales & Operations Planning |
| WMS | Warehouse Management System |
| DoR | Definition of Ready |
| DoD | Definition of Done |
| AC | Acceptance Criteria |

**Full glossary**: `knowledge/glossary.md`

---

## 15) Links

<!-- CUSTOMIZE: Add your project's URLs below -->

- Confluence: [Your Confluence URL]
- Jira: [Your Jira URL]
- GitHub: [Your GitHub repo URL]
- Trello: [Your Trello board URL] (optional)
- Other tools: [Add as needed]

---

## 16) Codex Integration (Parallel AI Tool)

<!-- OPTIONAL: If you use OpenAI Codex alongside Claude Code, configure this section.
     Otherwise, you can remove it entirely. -->

OpenAI Codex runs in VS Code alongside Claude Code. Both tools work on the same project but serve different purposes.

**Configuration files**:
- `AGENTS.md` — Codex reads this file for project context and instructions (equivalent of this CLAUDE.md)
- `.codex/config.toml` — Codex MCP server configuration

**Division of labour**:
- **Codex**: inline file editing, quick reviews, drafting text, batch file operations
- **Claude Code**: MCP-dependent tasks (Jira, Confluence), multi-agent orchestration, cross-epic coordination

**Sync rule**: When epics are added/removed/changed in Section 5 above, update the Active Epics table in `AGENTS.md` as well.
