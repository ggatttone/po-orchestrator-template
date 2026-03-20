# AGENTS.md — Codex Integration Context

> This file is read by OpenAI Codex (if running alongside Claude Code). It provides project context for inline editing tasks.

## Project Overview

This is a **multi-agent Product Owner orchestrator** built on Claude Code. The system helps a Product Owner manage parallel epics using 8 specialized AI agents and 18 slash-command skills.

## Architecture

- `CLAUDE.md` — Main orchestrator (routes tasks to agents)
- `.claude/agents/` — 8 sub-agents with specific scopes
- `.claude/skills/` — 18 slash commands for common workflows
- `knowledge/` — Shared reference files (glossary, standards, governance)
- `epics/` — Epic-as-directory pattern (one directory per workstream)

## Active Epics

<!-- Keep this table synchronized with CLAUDE.md Section 5 -->

| Epic ID | Epic Name | Status | Epic Key | Directory |
|---|---|---|---|---|
<!-- Add your epics here -->

## Division of Labour

- **Codex**: Inline file editing, quick reviews, drafting text, batch file operations
- **Claude Code**: MCP-dependent tasks (Jira, Confluence), multi-agent orchestration, cross-epic coordination

## Sync Rule

When epics are added/removed/changed in `CLAUDE.md` Section 5, update this table as well.
