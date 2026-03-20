# Customization Guide

How to adapt this template to your organization, program, and workflow.

## Priority order

Customize in this order for the fastest time-to-value:

1. CLAUDE.md (sections 2-5)
2. Profile
3. Glossary
4. Jira field mapping
5. Systems landscape
6. Governance model
7. Everything else

---

## 1. CLAUDE.md — The Orchestrator

### Section 2: Organisation Context

Replace the placeholder with a 3-5 sentence description of your company. Include:
- Industry and products/services
- Business model and key challenges
- Key locations
- Core value chain

### Section 3: Program Context

Describe the program or initiative you're working on:
- Program name and goal
- Main systems in scope
- How they integrate

### Section 4: Product Owner Profile

- Your name and role
- Main responsibilities
- Typical topics you work on
- Link to your detailed profile

### Section 5: Active Epics Registry

Add your epics. Each row needs:
- **Epic ID**: Short kebab-case identifier (e.g., `order-processing`)
- **Epic Name**: Human-readable name
- **Status**: Discovery / To Be Refined / Business Refined / Sprint Ready / In Progress
- **Epic Key**: Jira key (e.g., `PRJ-101`)
- **Directory**: Must match `epics/{epic-id}/`

After adding epics, create the matching directories:
```bash
cp -r epics/_template epics/your-epic-id
```

### Section 10: Multilingual Directives

If you communicate in a language other than English, add common phrases the system should recognize.

### Section 14: Quick Glossary

Add your 5-10 most-used abbreviations for quick reference.

### Section 15: Links

Add your project URLs (Confluence, Jira, Trello, etc.).

---

## 2. Knowledge Base Files

### glossary.md (Priority: HIGH)

Add your organization's terms organized by domain. The template includes standard Agile and ERP terms. Add:
- Company-specific abbreviations
- System names and their meanings
- Process terminology
- Role titles and their scope

### jira-field-mapping.md (Priority: HIGH)

Enter your Jira custom field IDs. To find them:
1. Go to Jira > Administration > Issues > Custom fields
2. Click a field > the URL contains the ID
3. Or use the API: `GET /rest/api/3/field`

### systems-landscape.md (Priority: MEDIUM)

Describe your technology stack:
- Core systems (ERP, CRM, WMS, planning)
- Integration architecture
- Key data flows

### governance.md (Priority: MEDIUM)

Map your governance model:
- Governance layers (steering group, program board, etc.)
- Decision authority at each level
- Communication cadence per layer

### stakeholder-map.md (Priority: MEDIUM)

Add your team members and stakeholders with their roles, contact preferences, and engagement patterns.

### way-of-working.md (Priority: LOW)

Adjust sprint cadence, stagegate definitions, and Definition of Ready / Definition of Done to match your delivery methodology.

### calendar-config.md (Priority: LOW)

Add your Outlook/Google Calendar ICS URL for automatic calendar integration in daily briefs.

---

## 3. Agent Tuning

Agents work well out of the box, but you can tune them:

### Adding system-specific research sources

In `.claude/agents/sparring-partner.md`, add your ERP/platform documentation URLs under "Research sources".

### Adjusting Confluence format

If your Confluence pages use a different layout, update the format references in `.claude/agents/requirements-analyst.md`.

### Changing model assignments

Some agents use `model: sonnet` for efficiency. Change to `model: inherit` (uses your default model) for higher quality at the cost of speed.

---

## 4. Adding New Skills

To create a new skill:

1. Create a directory: `.claude/skills/your-skill-name/`
2. Create `SKILL.md` with the skill's instructions
3. Add the skill to the `skills` list in the relevant agent's frontmatter
4. Add it to CLAUDE.md Section 8

---

## 5. Adding New Agents

To create a new agent:

1. Create `.claude/agents/your-agent.md` with YAML frontmatter
2. Define: name, description, tools, model, permissionMode, mcpServers
3. Add routing rules to CLAUDE.md Section 6
4. Add to the agent registry in CLAUDE.md Section 7

Required frontmatter:
```yaml
---
name: your-agent
description: "One-line description"
tools: Read, Edit, Write, Glob, Grep
model: inherit
permissionMode: acceptEdits
---
```
