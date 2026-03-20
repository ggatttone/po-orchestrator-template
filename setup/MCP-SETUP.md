# MCP Server Setup Guide

MCP (Model Context Protocol) servers connect Claude Code to external tools like Jira, Confluence, and Trello. The system works without them (local files only), but connecting MCP servers unlocks full functionality.

## Which servers do I need?

| Server | What it enables | Priority |
|---|---|---|
| **Atlassian** | Create Jira stories, update Confluence pages, query sprints | High — most skills benefit |
| **Trello** | Board management, card tracking | Medium — useful for kanban workflows |
| **OneDrive/SharePoint** | Read documents from cloud storage | Low — only needed for cloud docs |
| **n8n** | Workflow automation, multi-channel access | Low — advanced use case |

## Atlassian (Jira + Confluence)

The Atlassian MCP server runs as a Docker container.

### Prerequisites
- Docker installed and running
- Jira/Confluence API token (create at https://id.atlassian.com/manage-profile/security/api-tokens)

### Setup

1. Copy `.mcp.json.example` to `.mcp.json`
2. Fill in your credentials:
   ```json
   {
     "mcpServers": {
       "atlassian": {
         "env": {
           "JIRA_URL": "https://YOUR-INSTANCE.atlassian.net",
           "JIRA_USERNAME": "your-email@example.com",
           "JIRA_API_TOKEN": "your-api-token",
           "CONFLUENCE_URL": "https://YOUR-INSTANCE.atlassian.net/wiki",
           "CONFLUENCE_USERNAME": "your-email@example.com",
           "CONFLUENCE_API_TOKEN": "your-api-token"
         }
       }
     }
   }
   ```
3. Test: Start Claude Code and ask "list my Jira projects"

### Troubleshooting
- **Docker not running**: Start Docker Desktop
- **Authentication failed**: Regenerate your API token
- **No projects visible**: Check your Jira permissions

## Trello

### Prerequisites
- Trello account
- API key from https://trello.com/app-key
- Token generated from the API key page

### Setup

Add to `.mcp.json`:
```json
{
  "trello": {
    "command": "cmd",
    "args": ["/c", "npx", "-y", "@delorenj/mcp-server-trello"],
    "env": {
      "TRELLO_API_KEY": "your-api-key",
      "TRELLO_TOKEN": "your-token"
    }
  }
}
```

## OneDrive / SharePoint

Requires an Azure App Registration for authentication. See the [mcp-onedrive-sharepoint](https://github.com/seanoliver/mcp-onedrive-sharepoint) repository for setup instructions.

## n8n

Requires a running n8n instance with API access enabled.

Add to `.mcp.json`:
```json
{
  "n8n-mcp": {
    "command": "cmd",
    "args": ["/c", "npx", "n8n-mcp"],
    "env": {
      "N8N_BASE_URL": "https://your-n8n-instance.example.com",
      "N8N_API_KEY": "your-api-key"
    }
  }
}
```

## Running without MCP servers

All agents have a local-only fallback:
- Requirements are written to local markdown files instead of Jira
- Stories are drafted locally instead of created in Jira
- Confluence updates are shown as markdown diffs
- Calendar data uses manual events from `knowledge/calendar-events.md`

You can start without any MCP servers and add them later as needed.

## Security

- `.mcp.json` is listed in `.gitignore` — it will never be committed
- Never share your API tokens
- Consider using environment variables instead of hardcoding tokens
