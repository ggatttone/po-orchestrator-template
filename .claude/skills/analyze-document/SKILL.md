# /analyze-document

Analyze a document and produce a structured extraction of requirements, decisions, questions, and action items.

## Usage
`/analyze-document [epic-id] [file-path-or-description]`

## What this skill does

1. Identifies the document source:
   - **Local file**: Read from the specified path
   - **OneDrive file**: Read via OneDrive MCP (if configured)
   - **Confluence page**: Read via Atlassian MCP (page ID or title)
   - **Pasted text**: If the user pastes content directly, use that
2. Reads the epic context from `epics/{epic-id}/epic-context.md` (if epic-id is provided)
3. Reads `knowledge/glossary.md` and `knowledge/systems-landscape.md` for context
4. Delegates to the `document-analyst` agent for structured extraction
5. Outputs the analysis to the user
6. Optionally: saves the analysis as `{filename}-analysis.md` in the same directory as the source, or in `reference-docs/` for cloud documents

## Input

The user provides:
- **epic-id** (optional): The epic directory name under `epics/`. If omitted, the agent infers the epic from the document content.
- **file-path-or-description**: One of:
  - A local file path (e.g., `reference-docs/workshops/meeting-notes.txt`)
  - An OneDrive path (e.g., `OneDrive:path/to/document.pdf`)
  - A Confluence reference (e.g., `Confluence:PAGE_ID` or `Confluence:Page Title`)
  - The word "paste" — then the user pastes content in the next message

## Examples

```
/analyze-document order-processing reference-docs/design-docs/order-processing-BD.pdf
/analyze-document inventory-mgmt Confluence:123456789
/analyze-document transport paste
```

## Rules

- Never modify the original document.
- Never invent content not in the source document.
- Mark interpretations explicitly.
- Use terminology from `knowledge/glossary.md`.
- Translate non-English content to English in the analysis output.
- Preserve original quotes in parentheses when the translation is ambiguous.

## After analysis

Ask the user:
1. "Should I save this analysis to a file?"
2. "Should I integrate any items into the epic files?" (requirements -> requirements-log, decisions -> decisions-log, questions -> open-questions, actions -> action-items)

## Delegate to

Use the `document-analyst` agent for the actual analysis work.
