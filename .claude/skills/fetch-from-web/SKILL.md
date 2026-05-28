# /fetch-from-web

Fetch content from an authenticated web source (Outlook, Teams, SharePoint, OneDrive work, Confluence/Jira fallback) into `reference-docs/`, ready for downstream analysis.

## Usage

```
/fetch-from-web [source-type] [identifier] [--epic <epic-id>] [--since YYYY-MM-DD] [--until YYYY-MM-DD]
```

- **source-type** (optional): `outlook` | `sharepoint` | `teams` | `confluence` | `jira` | `auto` (default)
- **identifier**: URL, email subject/sender, Teams chat or channel name, SharePoint link, Confluence page ID, Jira issue key
- **--epic** (optional): epic directory name under `epics/`, used for cataloging in `inventory.md`
- **--since / --until** (optional): date range for Teams chat exports

## What this skill does

1. Resolves the engine: Playwright MCP (default) with the persistent Edge profile defined in `knowledge/web-fetch-conventions.md`.
2. Verifies the dedicated Edge profile is set up. If not, prompts the user to complete the first-time SSO setup (see § 3 of the conventions file).
3. Delegates to the `web-fetcher` agent.
4. The agent navigates the live web UI, captures the requested content, and saves it to the appropriate `reference-docs/<subfolder>/`.
5. Updates `reference-docs/inventory.md` and `analysis/web-fetch-log.md`.
6. Returns a hand-off pointing to the next skill (`/analyze-document`, `/workshop-followup`, `/meeting-followup`).

## Input examples

```
/fetch-from-web outlook "Workshop PRJ-101 transcript" --epic order-processing
/fetch-from-web sharepoint https://YOUR-INSTANCE.sharepoint.com/sites/Operations/Shared%20Documents/Design.pdf --epic inventory-mgmt
/fetch-from-web teams "PRJ - Stakeholder Name" --since 2025-05-01 --epic order-processing
/fetch-from-web confluence 123456789 --epic inventory-mgmt
/fetch-from-web auto https://outlook.office.com/mail/inbox/id/AAQ... --epic order-processing
```

## Rules

- **Read-only on the live web surface.** The agent never sends, replies, posts, uploads, or modifies remote state. See `knowledge/web-fetch-conventions.md` § 8.
- **Always try MCP first for Confluence/Jira.** Use browser only when the Atlassian MCP cannot return the required content.
- **Confirm before downloading large files.** Files > 5 MB trigger a confirmation prompt.
- **Confirm before exporting wide Teams ranges.** A range > 7 days triggers a confirmation prompt.
- **Never commit downloaded content as evidence into Jira/Confluence** without first running it through the analysis skills.
- **Naming convention**: `[Topic]_[Detail]_[YYYY-MM-DD].[ext]` (see `reference-docs/README.md`).

## After fetch

The agent always proposes the next skill, e.g.:

```
Saved 2 file(s):
- reference-docs/emails/Email_StakeholderName_Topic_2025-05-10.eml
- reference-docs/emails/Attachment_DesignDoc_v3_2025-05-10.pdf

Suggested next step:
  /analyze-document order-processing reference-docs/emails/Attachment_DesignDoc_v3_2025-05-10.pdf
```

## Delegate to

Use the `web-fetcher` agent for the actual browser-driven fetch.

## Related skills

- `/analyze-document` — structured extraction from a saved document
- `/workshop-followup` — process workshop transcripts/notes
- `/meeting-followup` — process meeting recordings/notes

## References

- `knowledge/web-fetch-conventions.md` — engine, profile, URLs, selectors, storage, safety
- `.claude/agents/web-fetcher.md` — agent definition
- See `docs/architecture-decisions.md` for the rationale for Playwright MCP
