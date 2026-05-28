# Jira Field Rules — Custom Fields, Formats, Limits

Canonical reference for Jira field IDs, value formats, and known limits in your projects on `YOUR-INSTANCE.atlassian.net`.
All agents must follow these rules when creating or updating Jira issues. Do not duplicate elsewhere.

<!-- CUSTOMIZE: Update the date when you adopt these conventions. -->

Date adopted: YYYY-MM-DD.

---

## 1. Custom field map

<!-- CUSTOMIZE: Replace the placeholder IDs (customfield_XXXXX) with the actual IDs from your Jira instance.
     Discover IDs via: GET /rest/api/3/field then grep for the display names.
     Maintain the centralised mapping in `knowledge/jira-field-mapping.md`. -->

| Field ID | Display name | Used on issue type | Format |
|---|---|---|---|
| `customfield_XXXXX` | Story | Story (required), Technical Story (optional) | string, ≤ 255 chars |
| `customfield_XXXXX` | Acceptance criteria | Story, Technical Story, Bug | ADF — checkbox list |
| `customfield_XXXXX` | High level Requirements | Story, Spike, BR, SD | ADF — narrative |
| `customfield_XXXXX` | Development report | Story, Technical Story | ADF — Scenarios (Given/When/Then) |
| `customfield_XXXXX` | Dependencies | all | string — cross-epic REQ IDs and Epic keys |
| `customfield_XXXXX` | Stakeholders | all | string — Roles / persons impacted |
| `customfield_XXXXX` | MoSCoW | Story | dropdown — see § 5 |
| `customfield_XXXXX` | Outstanding Issues | — | **Not always activatable per project** — verify before attempting to set |

## 2. Standard field formats

### `parent`
String form (not object):
```json
"parent": "PRJ-302"
```
Object form `{"parent": {"key": "PRJ-302"}}` works on some endpoints but is inconsistent. Default to string.

### `assignee`
- On **create issue**: account IDs of the form `712020:...` may fail intermittently. Use the email or display name.
- On **update issue**: display name works reliably.
- Look up account ID via `/rest/api/3/user/search?query={email}` if needed for mentions.

### `issuelinks` (type "Relates")
```json
{"add": {"type": {"id": "10003", "name": "Relates", "inward": "relates to", "outward": "relates to"}, "outwardIssue": {"key": "PRJ-XXXX"}}}
```

## 3. Story field — cap 255

Hard cap: **255 characters** (Jira-enforced; longer strings are silently truncated on save).
Operational cap: **≤ 240 characters** — leaves margin for safe save and downstream copy.

Required format on issue type `Story`:
```
As a [role ≤ 30 char], I want [capability ≤ 120 char], so that [benefit ≤ 80 char].
```

If the user story exceeds 240 chars in draft, condense by:
1. Replacing nominal phrases with verbs ("financial position visibility" → "see financial position")
2. Moving examples to the Description field
3. Splitting the Story into two stories if scope is genuinely dual

## 4. Story title (`summary`)

- Length: ≤ 80 characters (readability rule, not Jira-enforced)
- Language: English base; domain term-of-art preserved in original language only when no English equivalent is unambiguous (document in `knowledge/glossary.md`)
- No mixed-language syntactic phrasing
- Pattern by issue type: see `knowledge/atlassian-conventions.md` § 13

## 5. MoSCoW field — value rejection

The MoSCoW field may **reject** the literal string values `Must` and `Must Have` on `editJiraIssue` (despite the field option appearing in the metadata) — verify against your instance.

**Workaround**: skip the field during programmatic create/update and set it manually via the Jira UI. Do not retry with alternative spellings — the rejection is by value, not by case.

## 6. Comment body — Markdown → ADF rendering gotchas

When posting comments using markdown content via the Jira API, the conversion to ADF (Atlassian Document Format) drops or mangles several patterns:

| Pattern | Behaviour | Workaround |
|---|---|---|
| Emoji directly followed by bold (`✅ **Done**`) | Bold marker may be lost | Insert a space: `✅ **Done**` → `✅  **Done**` (two spaces) or use `(check) **Done**` |
| `1. 2. 3.` numbered list | Renumbered to `1. 1. 1.` if items have empty lines between them | Keep numbered items contiguous; use `*` for visually similar but un-numbered lists |
| Inline ` + ` characters (with surrounding spaces) | Dropped from output | Replace with `and` or remove spaces |
| Underscore in code (`my_var`) | Rendered as italic if preceded by space | Wrap in backticks: `` `my_var` `` |

## 7. Spike workflow — status transitions

<!-- CUSTOMIZE: Confirm transition IDs against your project workflow (via /rest/api/3/issue/{KEY}/transitions). -->

| From | To | Transition ID (sample) |
|---|---|---|
| `Backlog` | `In Progress` | **11** (Activate) |
| `In Progress` | `Done` | **61** (Finish) |
| `Done` | `Reopened` | varies — fetch via `getTransitionsForJiraIssue` |

On close, set `resolution` explicitly:
- `Done` — substantively answered
- `Won't Do` — not pursued (e.g., design-phase routed to REQ)
- `null` — anomaly; every closed Spike must have a resolution

See `knowledge/atlassian-conventions.md` § 3.

## 8. Issue types

<!-- CUSTOMIZE: Confirm via /rest/api/3/project/{KEY}. -->

Common issue types:

| Issue type | Subtask | Title prefix |
|---|---|---|
| Epic | no | none — `[Domain] - [Function]` pattern |
| Story | no | none — descriptive phrase |
| Spike | no | `OQ:` |
| Business Refinement | no | `BR:` |
| Solution Design | no | `SD:` |
| Technical Story | no | none |
| Task | no | `[Verb]:` (English) |
| Bug | no | `[Module]:` |
| Sub-task | yes | inherits parent style |

See title patterns in `knowledge/atlassian-conventions.md` § 13.

## 9. JQL patterns commonly needed

```jql
# Open spikes per epic (for merge-over-create audit)
project = PRJ AND issuetype = Spike AND statusCategory != Done AND "Epic Link" = PRJ-302

# Stories with empty Story field (regression flag)
project = PRJ AND issuetype = Story AND cf[XXXXX] is EMPTY

# Closed spikes with null resolution (anomaly)
project = PRJ AND issuetype = Spike AND status = Done AND resolution is EMPTY

# Issues linked to a REQ ID via High level Requirements
project = PRJ AND cf[XXXXX] ~ "REQ-EPIC-005"
```

## 10. REST endpoints (read-only, plan-mode-safe)

Using `.env` credentials (`ATLASSIAN_URL`, `ATLASSIAN_EMAIL`, `ATLASSIAN_API_TOKEN`):

```bash
# Single issue
GET {ATLASSIAN_URL}/rest/api/3/issue/{KEY}?fields=summary,status,customfield_XXXXX

# JQL search (v3 path: /search/jql, not /search)
GET {ATLASSIAN_URL}/rest/api/3/search/jql?jql=...&maxResults=N&fields=...

# Confluence page body (storage format)
GET {ATLASSIAN_URL}/wiki/api/v2/pages/{ID}?body-format=storage

# Confluence CQL search
GET {ATLASSIAN_URL}/wiki/rest/api/content/search?cql=type=page%20AND%20title~%22...%22

# Issue type metadata
GET {ATLASSIAN_URL}/rest/api/3/project/PRJ
```

---

## References

- Atlassian conventions (broader): `knowledge/atlassian-conventions.md`
- Centralised custom field mapping (your IDs): `knowledge/jira-field-mapping.md`
- Glossary: `knowledge/glossary.md`
