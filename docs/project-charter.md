# Project Charter — PO Operations Agent System

## Why this project exists

<!-- CUSTOMIZE: Describe the problem you are solving -->

As a Product Owner managing multiple parallel epics, you need structured support for:
- Translating workshop outputs into formal requirements
- Breaking requirements into sprint-sized Jira stories
- Tracking consistency across artifacts (requirements, stories, decisions, questions)
- Preparing for meetings and workshops efficiently
- Communicating status to stakeholders at different governance levels
- Maintaining traceability from workshop notes to Jira stories to Confluence pages

## Vision

A multi-agent AI system that acts as a force multiplier for the Product Owner, handling structured work (requirements, stories, consistency checks, communications) while the PO focuses on decision-making, stakeholder alignment, and strategic thinking.

## Architecture Overview

- **Orchestrator** (`CLAUDE.md`): Routes tasks to the right agent based on user intent
- **8 Sub-Agents**: Each specialized for a different PO task (requirements, stories, analysis, QA, planning, comms, sparring, workshops)
- **18 Skills**: Slash commands for common workflows
- **Knowledge Base**: Shared reference files (glossary, standards, governance, systems)
- **Epic-as-Directory**: Context isolation per workstream (7 standard files per epic)

## Design Principles

1. **Delegation, not execution**: The orchestrator routes, agents work
2. **Data integrity**: Never invent — only extract and structure
3. **Traceability**: Requirement -> Story -> Jira -> Confluence
4. **Audience matching**: Communications tailored to governance level
5. **Batch-one-at-a-time**: Process items sequentially with user confirmation
6. **Session continuity**: Check memory before rebuilding context
7. **Quality gates**: Every agent verifies output before delivery
