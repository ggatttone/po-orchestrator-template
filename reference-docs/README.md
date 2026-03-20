# Reference Documents

This directory stores external files (Word, PDF, PPT) that agents analyze or reference.

## Directory Structure

| Subdirectory | Contents |
|---|---|
| `workshops/` | Workshop transcripts, meeting notes, raw session outputs |
| `design-docs/` | Design documents, pre-reads, Business Designs, SDDs |
| `integrations/` | Integration specifications, API documentation |
| `processes/` | Process documentation, flow diagrams |
| `presentations/` | PowerPoint decks, slide materials |

## Naming Convention

`[Type]_[Topic]_[Date].ext`

Examples:
- `Workshop_InventorySync_2026-03-05.md`
- `BD_OrderProcessing_v2.pdf`
- `SDD_WMS-Integration_2026-02.docx`

## Usage

- The `/analyze-document` skill reads files from this directory
- The `/workshop-followup` skill saves processed notes here
- The `/meeting-followup` skill saves meeting summaries to `meetings/` (created automatically)
