# Quick Start Guide

Get up and running in 5 minutes.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed (`npm install -g @anthropic-ai/claude-code`)
- [VS Code](https://code.visualstudio.com/) (recommended but not required)
- [Git](https://git-scm.com/) installed
- An Anthropic API key (for Claude Code)

## Step 1: Fork and clone

1. Fork this repository on GitHub
2. Clone your fork:
   ```bash
   git clone https://github.com/YOUR-USERNAME/po-orchestrator-template.git
   cd po-orchestrator-template
   ```

## Step 2: Configure credentials (optional)

If you use Jira and Confluence:

1. Copy `.mcp.json.example` to `.mcp.json`
2. Copy `.env.example` to `.env`
3. Fill in your API tokens (see [MCP-SETUP.md](MCP-SETUP.md) for details)

If you don't use Jira/Confluence yet, skip this step. The system works with local files only.

## Step 3: Customize CLAUDE.md

Open `CLAUDE.md` and edit these sections:

- **Section 2** — Your organization (what your company does)
- **Section 3** — Your program (what initiative you're working on)
- **Section 4** — Your role (your responsibilities and typical topics)
- **Section 5** — Your epics (add your active workstreams)

## Step 4: Set up your profile

Edit `profile/your-name.md` with your background, expertise, and working style.

## Step 5: Customize the glossary

Edit `knowledge/glossary.md` and add your organization's specific terms.

## Step 6: Create your first epic

```bash
cp -r epics/_template epics/your-epic-name
```

Then edit `epics/your-epic-name/epic-context.md` with your epic's details.

## Step 7: Start using the system

Open the project in VS Code and start Claude Code. Try these commands:

```
# Load the example epic to see how things work
/switch-epic _example

# See a daily planning summary
/daily-brief

# Run a quality check on the example
/consistency-check _example

# Create a new requirement for your epic
/new-requirement your-epic-name "My first requirement"
```

## What to try next

1. **Import workshop notes**: Paste your workshop notes and ask Claude to extract requirements
2. **Run a priority review**: `/priority-review all` scores all your epics
3. **Prepare for a meeting**: `/sprint-prep your-epic-name`
4. **Draft a status update**: `/status-update "Program Board" all`

## Troubleshooting

**Claude Code doesn't recognize skills**: Make sure you're running Claude Code from the project root directory.

**MCP server errors**: Check that Docker is running (for Atlassian MCP) and credentials are correct in `.mcp.json`.

**Agent produces empty output**: Ensure your epic files have content (not just templates). Start with the example epic to see expected content.
