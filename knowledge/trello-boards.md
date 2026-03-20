# Trello Board Structure

<!-- CUSTOMIZE: Document your Trello boards, card conventions, and workflows -->

## Boards

| Board | Purpose | URL |
|---|---|---|
| PO Cockpit | Daily task management, priority tracking | [Your Trello URL] |
| Epic Tracker | Cross-epic status visibility | [Your Trello URL] |

## List Structure (PO Cockpit)

| List | Purpose |
|---|---|
| Inbox | New items to triage |
| This Week | Prioritized for current week |
| In Progress | Currently working on |
| Waiting | Blocked or waiting for someone |
| Done | Completed this sprint |

## Card Conventions

- **Title**: Short, action-oriented (same as action item text)
- **Labels**: Color-coded by epic or priority
- **Due date**: Matches action item due date
- **Description**: Link to epic file or Jira issue

## MCP Integration

The Trello MCP server enables:
- Creating cards from action items
- Moving cards between lists
- Reading board status for daily briefs
- Archiving completed cards

Configure in `.mcp.json` — see `setup/MCP-SETUP.md` for details.
