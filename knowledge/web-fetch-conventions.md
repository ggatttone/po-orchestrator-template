# Web Fetch Conventions

Conventions and operational rules for the `web-fetcher` agent and the `/fetch-from-web` skill. This file is the single source of truth for browser-automation configuration in this framework.

---

## 1. Engine

**Playwright MCP** (Microsoft, `@playwright/mcp`) is the canonical engine. The skill `browser-use` (system-level) is a fallback only for public sites where SSO is not required.

Rationale: see the relevant ADR in `docs/architecture-decisions.md`.

---

## 2. Browser profile (persistent)

To preserve Microsoft 365 SSO across sessions, Playwright MCP runs on Edge with a **dedicated user-data-dir**, separate from your personal Edge profile.

<!-- CUSTOMIZE: pick a profile location on your machine. The path below is an example. -->

| Setting | Value |
|---|---|
| Browser | Microsoft Edge (`msedge`) |
| User data dir | `<HOME>/.claude/browser-profiles/m365/` (example — choose your own location) |
| Headed by default | `true` (so MFA prompts are visible) |
| Isolated mode | Used only when the task requires a clean session (e.g., debugging selectors) |

**Why a dedicated profile**:
- The personal Edge profile may have unrelated cookies / extensions that interfere with automation
- Running automation while the user is using the personal profile causes profile-lock conflicts
- A dedicated profile gives reproducible behaviour for selectors and storage state

---

## 3. First-time setup (manual, once)

1. Ensure the dedicated profile directory exists (created automatically on first launch)
2. Ask Claude to launch Playwright MCP and navigate to `https://outlook.office.com`
3. Complete SSO + MFA manually in the visible browser window with your work account
4. Confirm login by checking that the inbox loads
5. Repeat once for `https://teams.microsoft.com` and `https://<your-tenant>.sharepoint.com` (the same SSO cookie usually covers all three; do this only if the second site asks for login)
6. Close the browser window — session cookies are now persisted in the profile

After this, all subsequent invocations of `/fetch-from-web` reuse the saved session.

---

## 4. Canonical entry URLs

<!-- CUSTOMIZE: replace YOUR-TENANT with your Microsoft 365 tenant and YOUR-INSTANCE with your Atlassian instance. -->

| Source | URL |
|---|---|
| Outlook Web (inbox) | `https://outlook.office.com/mail/inbox` |
| Outlook Web (search) | `https://outlook.office.com/mail/?search=<query>` |
| Teams Web (home) | `https://teams.microsoft.com` |
| Teams Web (chat) | `https://teams.microsoft.com/v2/?ctx=chat` |
| SharePoint (tenant root) | `https://YOUR-TENANT.sharepoint.com` |
| OneDrive (work) | `https://YOUR-TENANT-my.sharepoint.com/personal/<user>/_layouts/15/onedrive.aspx` |
| Confluence | `https://YOUR-INSTANCE.atlassian.net/wiki/spaces/<SPACE>/overview` |
| Jira (board) | `https://YOUR-INSTANCE.atlassian.net/jira/software/projects/<PROJECT>/board` |

Update this table whenever an entry URL changes (e.g., Microsoft renames a service).

**Note on search URL**: The `?search=<query>` URL pattern does NOT reliably open search results in the current Outlook version — it redirects to the inbox. Always interact with the search combobox element directly (see § 5).

---

## 5. Selector strategy

Outlook, Teams and SharePoint web UIs change CSS classes frequently. The agent must:

1. **Prefer semantic locators**: `getByRole`, `getByLabel`, `getByText`, `getByTestId`
2. **Avoid raw CSS class selectors** (e.g., `.ms-Button-flexContainer`) — these break on every UI update
3. **Wait for stable signals** (network idle, specific text, role visibility) instead of fixed timeouts
4. **Log selector failures** in `analysis/web-fetch-log.md` with a screenshot reference so we can update this file

Known stable anchors:

| Site | Anchor |
|---|---|
| Outlook | Role `searchbox` (top search), role `listbox` with name "Message list" |
| Teams | Role `tab` "Chat", role `tab` "Teams", `getByLabel("Type a new message")` |
| SharePoint | `getByRole("link", { name: "Documents" })`, `getByRole("button", { name: "Download" })` |
| Confluence | `getByRole("article")` for page body, `data-testid="page-title"` |

**Outlook search combobox — aria-label drift**:

The aria-label of the Outlook search combobox changes depending on mode and on the UI locale (English / Dutch / German / etc.):
- In inbox mode: the long localized prompt (e.g. `"Search for mail, meetings, files and more"`)
- In search-results mode: the short localized verb (e.g. `"Search"`)

As a result, `getByRole('combobox', { name: '<localized>' })` times out when Outlook is in inbox mode, and vice versa. The stable fallback selector that works in both modes and across locales is:

```
input[role="combobox"]
```

(First `input` with `role="combobox"` in the DOM — this is always the search box.)

Sequence to reliably type a search query:
1. `page.click('input[role="combobox"]')` to focus
2. `Ctrl+A` then `Delete` to clear any stale value from a previous search
3. `page.fill('input[role="combobox"]', '<query>')` to set the value
4. `Enter` to submit

Also: Outlook may show calendar reminder popups that overlap the UI. Dismiss them with `getByRole('button', { name: /close|dismiss|sluiten|alles negeren/i })` before interacting with the search box.

---

## 6. Storage convention

Files fetched by the agent land in `reference-docs/` under typed subfolders:

| Source type | Target folder | Naming |
|---|---|---|
| Email + attachments | `reference-docs/emails/` | `Email_<Topic>_<YYYY-MM-DD>.eml` (+ `Attachment_<Filename>_<YYYY-MM-DD>.<ext>`) |
| SharePoint file | `reference-docs/sharepoint-fetched/` | `SP_<Topic>_<Detail>_<YYYY-MM-DD>.<ext>` |
| OneDrive (work) file | `reference-docs/sharepoint-fetched/` | `OD_<Topic>_<Detail>_<YYYY-MM-DD>.<ext>` |
| Teams chat export | `reference-docs/teams-chats/` | `Teams_<ChatOrChannel>_<YYYY-MM-DD>.md` |
| Teams channel post | `reference-docs/teams-chats/` | `TeamsChannel_<Team>_<Channel>_<YYYY-MM-DD>.md` |
| Confluence fallback | `reference-docs/design-docs/` | `Confluence_<PageID>_<YYYY-MM-DD>.html` |
| Jira fallback | `reference-docs/integrations/` | `Jira_<IssueKey>_<YYYY-MM-DD>.md` |

After saving, the agent appends a one-line entry to `reference-docs/inventory.md` with: filename, source URL or identifier, fetch date, related epic (if any), short description.

---

## 7. Size and safety thresholds

| Threshold | Action |
|---|---|
| File > 5 MB | Ask the user for confirmation before downloading |
| Email thread > 50 messages | Save full thread but produce an additional `_summary.md` next to it |
| Teams export > 7 days range | Ask the user to confirm the date range |
| Total download in one invocation > 50 MB | Pause and ask user before continuing |
| Sensitive sources (HR, Legal, Finance shares) | Always ask before fetching, regardless of size |

---

## 8. Write actions — strictly forbidden

The `web-fetcher` agent must NEVER:

- Send emails, reply to emails, forward emails
- Post messages or reactions in Teams chats or channels
- Modify documents in SharePoint or OneDrive (upload, rename, delete, edit)
- Comment, edit or transition Jira issues
- Edit Confluence pages
- Click "Approve", "Submit", "Pay" or any actionable button outside read/download flows

The agent is **read-only** on the live web surface. The only writes it performs are to the local `reference-docs/` directory.

If the user explicitly asks for a write action via browser (e.g., "send this email"), the agent must refuse and suggest the appropriate MCP-based path (Atlassian MCP for Jira/Confluence, manual user action for Outlook/Teams).

### Preserving read/unread state

Opening a mail in the Outlook reading pane **auto-marks it read** — a side-effect on remote state. To avoid or undo it:

- To mark a mail unread **without opening it**: right-click the row in the results / message list → `Mark as unread` (or localized equivalent). This does NOT open the reading pane, so it does not re-mark it read.
- The keyboard shortcut `u` is unreliable via Playwright — use the right-click menu action.
- If a mail had to be opened to read its content and it was unread before, restore it afterwards via the same right-click → `Mark as unread`.
- Decide upfront with the user whether opened mails should be left read or restored to unread, and record the choice in `analysis/web-fetch-log.md`.

---

## 9. Logging

Every invocation of `/fetch-from-web` produces a log entry in `analysis/web-fetch-log.md` (create the directory and file on first run if they don't exist):

```markdown
## YYYY-MM-DD HH:mm — <skill invocation>

- **Source**: outlook | sharepoint | teams | confluence
- **Identifier**: <query, URL, page ID>
- **Epic**: <epic-id or "n/a">
- **Files saved**: <list>
- **Selectors used**: <main role/label locators>
- **Issues**: <selector failures, timeouts, auth prompts>
```

This log is also useful for spotting UI drift early (recurring selector failures = update `web-fetch-conventions.md` § 5).

---

## 10. Fallback to `browser-use` skill

Use the system-level `browser-use` skill instead of Playwright MCP when:

- The target is a public site (no SSO required) and a one-off action is needed
- Playwright MCP is unavailable (server down, package install issue)
- The task requires visual reasoning over a page that the agent cannot describe via the accessibility tree alone

Document any use of `browser-use` in the log so we can decide if it should be promoted to a proper Playwright flow.

---

## 11. Maintenance checklist

Review this file every 3 months, or sooner if:

- Microsoft renames a service (URL changes)
- Outlook / Teams / SharePoint roll out a major UI redesign
- A new MCP variant is released that simplifies the setup
- Selectors fail repeatedly (log entries in `analysis/web-fetch-log.md`)
- The tenant DLP policy (§ 12) changes — re-test download + viewer rendering

---

## 12. DLP attachment constraint (tenant-specific awareness)

<!-- CUSTOMIZE: verify whether your tenant enforces Microsoft Purview / DLP. If it does, the constraints below
     materially limit attachment handling via browser session and you must plan for them. -->

Some Microsoft 365 tenants enforce a Microsoft Purview / DLP policy that materially limits attachment handling via the browser session. Typical message in the Outlook viewer:

> *"Downloading, printing or syncing using this device is not allowed in your organization. Use a domain-joined device."*

| Method | Result on a DLP-protected tenant |
|---|---|
| Binary download (`.docx`/`.xlsx`/`.pdf`) | **Blocked** — no local file can be saved |
| Office Online viewer (embedded in Outlook) | Document rendered as **raster image** — no text in DOM/accessibility tree |
| "Open in new window" | Same raster-image rendering — does NOT bypass |
| Immersive Reader | **Exposes text** word-by-word in the a11y tree; loads progressively on scroll (accurate but verbose) |
| Screenshot-OCR (agent reads rendered image) | **Works**; best info-density per step for short docs; costly for long/multi-page docs |
| `browser_evaluate` into viewer iframe | Blocked (cross-origin) |

**Working procedure for DLP-protected attachments**:
1. Short docs (≤ ~2 pages): screenshot-OCR — open attachment, screenshot the rendered page(s), Read the image, transcribe verbatim.
2. Long/multi-page docs: do NOT brute-force OCR inline. Prefer, in order:
   a. Ask the user to copy key sections from a domain-joined context into a local file.
   b. Check whether the document is mirrored in **Confluence** — reachable via Atlassian MCP without the DLP image-render.
   c. Run a **dedicated, budgeted OCR-extraction sub-task** (Immersive Reader + a11y snapshot, page by page) if a + b are unavailable.
3. Always record in the saved `.md`: which attachments were extracted, which were not, and why; include the recommendation.
4. Email/Teams **bodies** are NOT affected by this DLP rule — they extract normally from the accessibility tree.

**Auth note**: the persistent Edge profile SSO cookie typically expires after ~6 days; Microsoft requires interactive password + MFA again (account pre-filled). Expect roughly weekly interactive re-auth; the agent must stop and ask the user to complete it in the headed window (never handle credentials).
