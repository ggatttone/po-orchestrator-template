# Jira Field Mapping

Configure your Jira instance's custom field IDs here. Agents reference this file when creating or updating Jira issues.

## How to find your field IDs

1. **Jira UI**: Administration > Issues > Custom fields > Click field > URL contains field ID (e.g., `customFieldId=10040`)
2. **REST API**: `GET https://[your-instance].atlassian.net/rest/api/3/field` — returns all fields with IDs
3. **MCP**: Use `mcp__atlassian__jira_search_fields` to search for specific field names

## Custom field mapping

<!-- CUSTOMIZE: Update these field IDs to match your Jira instance -->

| Content | Jira Field Name | Field ID | Used by |
|---|---|---|---|
| User Story | Story | `customfield_XXXXX` | jira-story-engineer, requirements-analyst |
| Acceptance Criteria | Acceptance criteria | `customfield_XXXXX` | jira-story-engineer, requirements-analyst, triage |
| High-level Requirements | High level Reqs | `customfield_XXXXX` | jira-story-engineer |
| Development Report | Dev report | `customfield_XXXXX` | jira-story-engineer |
| Dependencies | Dependencies | `customfield_XXXXX` | jira-story-engineer |
| Stakeholders | Stakeholders | `customfield_XXXXX` | jira-story-engineer |
| MoSCoW Priority | MoSCoW | `customfield_XXXXX` | — |

## Issue types

<!-- CUSTOMIZE: Update these if your Jira uses different issue type IDs -->

| Type | ID | Used for |
|---|---|---|
| Story | (default) | Requirements, features |
| Spike | `XXXXX` | Investigation items, open questions |
| Task | (default) | Non-feature work |
| Bug | (default) | Defects |

## Jira parent format

When creating child issues under an epic:
```json
{
  "parent": "PRJ-101"
}
```
Note: Use a string with the epic key, not an object.

## Assignee format

- When **creating** issues: assignees may not be accepted via account ID — leave blank or use display names
- When **updating** issues: use display names in the assignee field
