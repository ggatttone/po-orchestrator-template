# Elicitation Question Bank

This file contains the standard Minimum Question Battery used when generating `/elicitation-pack` documents.

**Who edits this file**: The Product Owner can add, remove, or reword questions at any time without touching the agent or skill file.

**How agents use this file**: The `workshop-designer` agent reads this file when generating an elicitation pack. It filters questions by the domain tags below and inserts them into the Minimum Question Battery section of the output document.

**Tags**:
- `[cross-cutting]` — always included, regardless of epic type
- `[ERP]` — included for ERP epics (Purchase Orders, Sales Orders, inventory, import/export)
- `[WMS]` — included for WMS integration epics (stock levels, location transfers, lot tracking)
- `[planning]` — included for S&OP / planning epics (forecasting, supply planning)

---

## Category 1: Definition (3 questions)

| ID | Tag | Question |
|---|---|---|
| D1 | [cross-cutting] | What is **[topic]** in your own words — as you would explain it to a new colleague on day 1? |
| D2 | [cross-cutting] | What is the single most important thing that must go right in this process? |
| D3 | [cross-cutting] | What is the biggest difference between how you currently do this and how you think it should work in the new system? |

---

## Category 2: Boundaries and Process Map (4 questions)

| ID | Tag | Question |
|---|---|---|
| B1 | [cross-cutting] | What event or signal **starts** this process? (e.g., a customer order, a delivery confirmation, a calendar date, a manual trigger) |
| B2 | [cross-cutting] | What event or signal **ends** this process? How does the team know it is complete? |
| B3 | [ERP] | Which other process or team must complete something **before** this process can start? What exactly do they provide? |
| B4 | [ERP] | Which process or team **depends on the output** of this process before they can continue? |

---

## Category 3: Roles and RACI (4 questions)

| ID | Tag | Question |
|---|---|---|
| R1 | [cross-cutting] | Who **does** this process today — describe the role, not the person? |
| R2 | [cross-cutting] | Who **approves or confirms** the output? (the person ultimately accountable for the result) |
| R3 | [ERP] | Who is **consulted** when something goes wrong or an exception happens? |
| R4 | [cross-cutting] | Who needs to **know the result** but does not take action themselves? (informed parties) |

---

## Category 4: Volumes, Timings, Exceptions (3 questions)

| ID | Tag | Question |
|---|---|---|
| V1 | [cross-cutting] | How often does this process run — per day, per week, or triggered by an event? Are there seasonal peaks? |
| V2 | [cross-cutting] | What is the most **common error or exception** in this process today? How often does it occur? |
| V3 | [ERP] | When you have an urgent or exceptional case (peak demand, rush order, error correction), how do you handle it **differently** from the standard flow? |

---

## Category 5: Systems, Data, and Artifacts (3 questions)

| ID | Tag | Question |
|---|---|---|
| S1 | [cross-cutting] | Which **systems or tools** do you use today for this process? List all — including Excel, email, WhatsApp, or paper. |
| S2 | [ERP] | What **data or documents** do you need to start this process? (inputs — e.g., purchase order, delivery note, lot number) |
| S3 | [ERP] | What **data or documents** does this process produce? (outputs — e.g., confirmation, report, invoice, stock update) |

---

## Category 6: Value and Metrics (3 questions)

| ID | Tag | Question |
|---|---|---|
| M1 | [cross-cutting] | Why does this process exist? What would break or go wrong if it stopped? |
| M2 | [cross-cutting] | Do you measure how well this process works today? If yes: what do you measure and how? If no: what signals tell you it is going well or badly? |
| M3 | [cross-cutting] | What is the most **painful part** of the current way of working? What workaround do you use today? |

---

## WMS-Specific Additions

Use these questions **in addition to** the 20 core questions above, when the epic is in the WMS integration domain (stock levels, location transfers, lot tracking).

| ID | Tag | Question |
|---|---|---|
| W1 | [WMS] | What happens in the warehouse **between receiving a shipment** and confirming it in the system? Who performs each step, and what can go wrong? |
| W2 | [WMS] | What is the current procedure when the system shows a **discrepancy** versus the physical stock? Who is notified, and how is it resolved? |

---

## Planning-Specific Additions

Use these questions **in addition to** the 20 core questions above, when the epic is in the S&OP / planning domain (forecasting, supply planning, demand signals).

| ID | Tag | Question |
|---|---|---|
| P1 | [planning] | What **demand signals** does your team receive today, and from which source? How reliable are they? |
| P2 | [planning] | How far ahead do you plan, and what is the **planning cycle frequency**? When during the week/month do you make planning decisions? |

---

## How to Add Custom Questions

1. Add a new row to the relevant category table.
2. Use a new ID: for a new cross-cutting question in Category 2, use B5; for a new ERP question, use B3b or start a new sub-section.
3. Add the correct tag: `[cross-cutting]`, `[ERP]`, `[WMS]`, or `[planning]`.
4. Write the question so business users can answer it with a sentence or two — avoid yes/no questions.
5. No other changes are needed — the agent picks up questions automatically from this file.
