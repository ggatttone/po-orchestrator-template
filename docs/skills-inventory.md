# Skills Inventory

Complete inventory of agents, skills, and integrations in this system.

## Sub-Agents (8)

| Agent | File | Scope | Model |
|---|---|---|---|
| requirements-analyst | `.claude/agents/requirements-analyst.md` | Raw input -> structured requirements | inherit |
| jira-story-engineer | `.claude/agents/jira-story-engineer.md` | Requirements -> Jira stories | inherit |
| document-analyst | `.claude/agents/document-analyst.md` | Document extraction (read-only) | inherit |
| review-qa | `.claude/agents/review-qa.md` | Consistency checks (read-only) | sonnet |
| pm-support | `.claude/agents/pm-support.md` | Sprint prep, planning, priorities | sonnet |
| stakeholder-comms | `.claude/agents/stakeholder-comms.md` | Audience-tailored communications | sonnet |
| sparring-partner | `.claude/agents/sparring-partner.md` | Critical thinking (read-only) | sonnet |
| workshop-designer | `.claude/agents/workshop-designer.md` | Elicitation packs + questionnaire analysis | inherit |

## Skills (19)

| Skill | Agent | Category |
|---|---|---|
| `/new-requirement` | requirements-analyst | Requirements |
| `/new-story` | jira-story-engineer | Stories |
| `/switch-epic` | orchestrator | Navigation |
| `/analyze-document` | document-analyst | Analysis |
| `/consistency-check` | review-qa | Quality |
| `/workshop-followup` | document-analyst | Workshops |
| `/action-review` | pm-support | Planning |
| `/sprint-prep` | pm-support | Planning |
| `/status-update` | stakeholder-comms | Communications |
| `/meeting-followup` | document-analyst | Meetings |
| `/epic-summary` | stakeholder-comms | Communications |
| `/daily-brief` | pm-support | Planning |
| `/weekly-plan` | pm-support | Planning |
| `/priority-review` | pm-support | Planning |
| `/timeline-sync` | pm-support | Planning |
| `/next-actions` | pm-support | Planning |
| `/elicitation-pack` | workshop-designer | Workshops |
| `/questionnaire-results` | workshop-designer | Workshops |
| `/triage` | pm-support | Triage |

## MCP Servers

| Server | Purpose | Required |
|---|---|---|
| Atlassian | Jira + Confluence | Recommended |
| Trello | Board management | Optional |
| OneDrive | Cloud documents | Optional |
| n8n | Workflow automation | Optional |
