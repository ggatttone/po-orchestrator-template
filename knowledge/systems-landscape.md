# Systems Landscape

<!-- CUSTOMIZE: Describe your technology stack, integrations, and key data flows -->

## Core Systems

| System | Type | Purpose | Vendor |
|---|---|---|---|
| [Your ERP] | ERP | Core business processes (procurement, sales, inventory) | [Vendor] |
| [Your CRM] | CRM | Customer relationship management, sales pipeline | [Vendor] |
| [Your WMS] | WMS | Warehouse operations, stock management | [Vendor] |
| [Your Planning Tool] | S&OP | Demand forecasting, supply planning | [Vendor] |

## Integration Architecture

```
[Your CRM] <--> [Your ERP] <--> [Your WMS]
                    |
              [Your Planning Tool]
```

<!-- Describe key integration points:
- What data flows between systems?
- Real-time or batch?
- API-based or file-based?
-->

## Platform Capabilities

Use this section to document what your platform supports out-of-the-box vs. what needs customization. The `requirements-analyst` agent references this for baseline analysis.

| Capability | Support Level | Notes |
|---|---|---|
| [Capability] | OOTB / Config / Custom / Not Supported | [Details] |

## MCP Server Integrations

| Server | Status | Purpose |
|---|---|---|
| Atlassian | [Operational/Not configured] | Jira + Confluence access |
| Trello | [Operational/Not configured] | Board management |
| OneDrive | [Operational/Not configured] | Cloud document access |
| n8n | [Operational/Not configured] | Workflow automation |

## Online Documentation

<!-- CUSTOMIZE: Add links to your vendor documentation for sparring-partner research -->

| System | Documentation URL | Notes |
|---|---|---|
| [Your ERP] | [URL] | [Access level] |
| [Your Platform] | [URL] | [Access level] |
