# Architecture Guide

How the PO Orchestrator system is designed and why.

## Core Pattern: Orchestrator + Sub-Agents

The system uses a **delegation pattern**:

1. You give Claude Code a task (natural language or slash command)
2. `CLAUDE.md` (the orchestrator) identifies the task type
3. The orchestrator routes to the correct sub-agent
4. The agent reads context, does the work, produces output
5. You review and approve

The orchestrator **never does work itself** — it only routes. This keeps each agent focused and maintainable.

## Why this architecture?

| Design Choice | Alternative | Why we chose this |
|---|---|---|
| Orchestrator + agents | Single monolithic prompt | Separation of concerns, easier to maintain and extend |
| Epic-as-directory | Single file per epic | Context isolation, prevents cross-contamination |
| Knowledge outside `.claude/` | Inside `.claude/` | Shared access for all tools (Codex, manual editing) |
| Skills as slash commands | Natural language only | Predictable, discoverable, consistent output |
| Section-per-requirement | Table-based layout | Better for Confluence rendering, richer content per requirement |
| Local files first | Jira/Confluence first | Works offline, version controlled, no vendor lock-in |

## Directory Structure

```
project-root/
|
|-- CLAUDE.md                    # Orchestrator — the brain
|-- AGENTS.md                    # Codex integration (optional)
|
|-- .claude/
|   |-- agents/                  # 8 agent specifications
|   |-- skills/                  # 18 skill definitions
|   |-- shared-rules.md          # Universal agent rules
|   |-- settings.json            # Claude Code settings
|
|-- knowledge/                   # Shared reference files
|   |-- glossary.md              # Your terminology
|   |-- requirement-standards.md # How to write requirements
|   |-- governance.md            # Your governance model
|   |-- systems-landscape.md     # Your technology stack
|   |-- jira-field-mapping.md    # Jira custom field IDs
|   |-- ...                      # 9 more reference files
|
|-- epics/                       # One directory per workstream
|   |-- _template/               # Template for new epics (7 files)
|   |-- _example/                # Fully populated example
|   |-- your-epic-name/          # Your actual epics
|
|-- profile/                     # Your professional profile
|-- docs/                        # System documentation
|-- reference-docs/              # External documents (Word, PDF)
|-- setup/                       # Setup and customization guides
```

## Agent Design

Each agent is a markdown file with:
- **YAML frontmatter**: Tools, model, permissions, MCP servers
- **Role description**: What the agent does and doesn't do
- **Workflow**: Step-by-step instructions
- **Output format**: Exact template for the output
- **Rules**: Constraints and quality checks
- **Quality checklist**: Verification before final output

Agents reference shared rules (`.claude/shared-rules.md`) and knowledge files, creating a consistent experience across all tasks.

## Epic-as-Directory Pattern

Each epic gets its own directory with 7 standard files:

| File | Purpose | Written by |
|---|---|---|
| `epic-context.md` | Scope, stakeholders, status | You (PO) |
| `requirements-log.md` | All requirements | requirements-analyst |
| `story-index.md` | Jira story keys | jira-story-engineer |
| `decisions-log.md` | Captured decisions | requirements-analyst |
| `open-questions.md` | Unresolved items | requirements-analyst |
| `action-items.md` | Tasks and follow-ups | pm-support |
| `elicitation-pack-template.md` | Workshop prep | workshop-designer |

This pattern ensures:
- **Context isolation**: Working on Epic A doesn't pollute Epic B
- **Cross-epic scanning**: Agents can read other epics to find dependencies
- **Consistency**: Every epic has the same structure

## Priority Scoring Algorithm

The `pm-support` agent uses a **5-dimensional scoring algorithm** (max 100 points) to rank epics:

| Dimension | Max Points | Based on |
|---|---|---|
| Urgency | 30 | Nearest deadline type and proximity |
| Dependencies | 25 | Cross-epic blocking relationships |
| Stakeholder Impact | 20 | Seniority of involved stakeholders |
| Sprint Alignment | 15 | Current sprint status |
| Open Questions | 10 | Fewer blockers = higher score |

This ensures priority decisions are data-driven, not arbitrary.

## Traceability Chain

The system maintains a full traceability chain:

```
Workshop Notes / Documents
    |  (document-analyst extracts)
    v
Draft Requirements
    |  (requirements-analyst formalizes)
    v
REQ-[EPIC]-[NNN] in requirements-log.md
    |  (auto-creates)
    v
Jira Story (PRJ-XXX) + Confluence Page
    |  (jira-story-engineer breaks down)
    v
Sprint-sized Stories in story-index.md
```

Every artifact links back to its source, enabling audits via `/consistency-check`.

## Session Continuity

The system uses Claude Code's auto-memory (`MEMORY.md`) to persist context across conversations:
- Progress on multi-step tasks
- Lessons learned (what worked, what didn't)
- User preferences and working style
- Key technical references

This means you don't need to re-explain context when starting a new session.

## Extending the System

### Adding a new agent
1. Create `.claude/agents/your-agent.md` with frontmatter + instructions
2. Add routing rule to CLAUDE.md Section 6
3. Add to registry in CLAUDE.md Section 7

### Adding a new skill
1. Create `.claude/skills/your-skill/SKILL.md`
2. Add to the agent's `skills` frontmatter
3. Add to CLAUDE.md Section 8

### Adding a knowledge file
1. Create the file in `knowledge/`
2. Add to CLAUDE.md Section 11
3. Reference it in the relevant agent files
