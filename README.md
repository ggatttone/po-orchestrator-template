# PO Orchestrator Template

> A multi-agent Claude Code orchestrator for Product Owners managing parallel workstreams. Fork, customize, operate.

## What is this?

This is a ready-to-use **AI assistant system** built on [Claude Code](https://docs.anthropic.com/en/docs/claude-code) that helps Product Owners manage complex programs with multiple parallel epics.

It uses an **orchestrator pattern**: a central file (`CLAUDE.md`) routes your requests to 8 specialized AI agents, each designed for a specific PO task. You interact through natural language and 18 slash commands.

**Who is this for?**
- Product Owners managing 3+ parallel workstreams
- POs working with Jira, Confluence, and Agile/Scrum methodologies
- Anyone who needs structured support for requirements, stories, planning, and stakeholder communication

## Architecture

```
You (Product Owner)
    |
    v
CLAUDE.md (Orchestrator)
    |
    +-- requirements-analyst    --> Workshop notes -> structured requirements
    +-- jira-story-engineer     --> Requirements -> sprint-sized Jira stories
    +-- document-analyst        --> Design docs -> structured analysis
    +-- review-qa               --> Consistency checks across artifacts
    +-- pm-support              --> Sprint prep, daily briefs, priority scoring
    +-- stakeholder-comms       --> Audience-tailored status updates
    +-- sparring-partner        --> Critical thinking, assumption challenging
    +-- workshop-designer       --> Business elicitation packs & questionnaire analysis
    |
    +-- 18 Slash Commands       --> Quick access to common workflows
    +-- Knowledge Base          --> Shared glossary, standards, governance
    +-- Epic Directories        --> Context isolation per workstream
```

## What you get

| Component | Count | Description |
|---|---|---|
| Orchestrator | 1 | `CLAUDE.md` — routes tasks to the right agent |
| Sub-Agents | 8 | Specialized AI agents for different PO tasks |
| Skills | 18 | Slash commands for common workflows |
| Knowledge Base | 14 | Configurable reference files |
| Epic Template | 7 | Standard files per workstream |
| Example Epic | 1 | Fully populated example to learn from |
| Setup Guides | 4 | Quickstart, customization, MCP, architecture |

## Quick Start (5 minutes)

1. **Fork** this repository
2. **Install** Claude Code: `npm install -g @anthropic-ai/claude-code`
3. **Clone** your fork locally
4. **Configure credentials**: Copy `.mcp.json.example` to `.mcp.json` and add your Jira/Confluence API tokens
5. **Customize CLAUDE.md**: Edit sections 2-5 with your organization, program, role, and epics
6. **Set up your profile**: Edit `profile/your-name.md`
7. **Create your first epic**: Copy `epics/_template/` to `epics/your-epic-name/`
8. **Start Claude Code** in the project directory and try:
   - `/switch-epic _example` — Load the example epic
   - `/daily-brief` — See a daily planning summary
   - `/consistency-check _example` — Run a quality audit

See [setup/QUICKSTART.md](setup/QUICKSTART.md) for detailed instructions.

## Available Slash Commands

| Command | What it does |
|---|---|
| `/new-requirement [epic] [title]` | Create a structured requirement with auto Jira story |
| `/new-story [epic] [req-id]` | Break a requirement into sprint-sized stories |
| `/switch-epic [epic]` | Load an epic's full context |
| `/analyze-document [epic] [file]` | Extract structured content from a document |
| `/consistency-check [epic]` | Run coverage and consistency audit |
| `/workshop-followup [epic]` | Distribute workshop outcomes to epic files |
| `/action-review [epic\|all]` | Audit action items across epics |
| `/sprint-prep [epic]` | Assess story readiness for sprint |
| `/status-update [audience] [epic\|all]` | Draft audience-tailored status report |
| `/meeting-followup [epic]` | Capture and distribute meeting outcomes |
| `/epic-summary [epic\|all]` | High-level epic status snapshot |
| `/daily-brief` | Personal daily planning summary |
| `/weekly-plan` | Cross-epic weekly view |
| `/priority-review [epic\|all]` | Score and rank epics by priority (0-100) |
| `/timeline-sync` | Align release and development timelines |
| `/next-actions [now\|today\|this-week]` | Filtered action item summary |
| `/elicitation-pack [epic]` | Generate pre-workshop business questionnaire |
| `/questionnaire-results [epic]` | Analyze collected questionnaire responses |
| `/triage [epic]` | 7-step epic triage process |

## MCP Server Integrations

The system works with local files by default. For full functionality, connect these MCP servers:

| Server | Purpose | Required? |
|---|---|---|
| **Atlassian** (Jira + Confluence) | Create stories, update pages, query sprints | Recommended |
| **Trello** | Board management, card tracking | Optional |
| **OneDrive/SharePoint** | Read cloud documents | Optional |
| **n8n** | Workflow automation, multi-channel access | Optional |

See [setup/MCP-SETUP.md](setup/MCP-SETUP.md) for installation instructions.

## Customization Guide

The system is designed to be forked and customized. Key customization points:

1. **CLAUDE.md** sections 2-5: Your organization, program, role, and epics
2. **Knowledge base**: Glossary, systems landscape, governance model
3. **Jira field mapping**: Your custom field IDs
4. **Agent behavior**: Tune agent prompts for your domain

See [setup/CUSTOMIZATION.md](setup/CUSTOMIZATION.md) for detailed instructions.

## Project Origin

This template was extracted from a production system used by a Product Owner at a food distribution company, managing 18 parallel ERP implementation epics. The patterns and workflows have been refined over months of daily use.

The original system was built iteratively through 9+ phases of development, documented in Architecture Decision Records (ADRs). All company-specific data has been removed and replaced with templates and examples.

## License

MIT — see [LICENSE](LICENSE)

## Contributing

Contributions are welcome. Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request with a clear description

For bug reports or feature requests, open an issue.
