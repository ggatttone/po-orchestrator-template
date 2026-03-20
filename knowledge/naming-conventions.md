# Jira Naming Conventions

<!-- CUSTOMIZE: Document your Jira naming patterns here -->

## Epic Naming Patterns

Epics should follow a consistent naming pattern. Common patterns observed in Jira instances:

| Pattern | Example | When to use |
|---|---|---|
| Domain-Specific | "Procurement - Indirect Purchasing" | When the epic is part of a domain with multiple sub-epics |
| Plain Noun | "Export" | When the epic is a standalone domain |
| Integration | "WMS Integration - Stock Levels" | When the epic involves system integration |

## Story Naming

- Start with an action verb
- Keep under 80 characters
- Reference the business capability, not the technical implementation
- Example: "Track lot numbers on inbound purchase orders"

## Spike Naming

- Prefix with "OQ: " for open-question spikes
- Prefix with "Spike: " for general investigation
- Example: "OQ: Define acceptable stock sync latency"

## Requirement ID Format

`REQ-[EPIC-SHORT]-[NNN]`

Where:
- `EPIC-SHORT` is a 2-5 character abbreviation of the epic name
- `NNN` is a zero-padded sequential number

Examples:
- `REQ-WIM-001` (Warehouse Inventory Management)
- `REQ-ORD-003` (Order Processing)

## Jira Project Keys

<!-- CUSTOMIZE: List your Jira project keys -->

| Project | Key | Used for |
|---|---|---|
| [Your ERP Project] | [KEY] | ERP implementation stories |
| [Your Planning Project] | [KEY] | Planning/S&OP stories |

## Labels

<!-- CUSTOMIZE: Document any standard Jira labels you use -->

| Label | When to apply |
|---|---|
| [label] | [criteria] |
