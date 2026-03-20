# Requirements Log — Warehouse Inventory Management

| ID | Title | Importance | Jira Issue | Status |
|---|---|---|---|---|
| REQ-WIM-001 | Real-time stock sync between ERP and WMS | HIGH | PRJ-201 | Draft |
| REQ-WIM-002 | Lot tracking and traceability | HIGH | PRJ-202 | Draft |
| REQ-WIM-003 | Location transfer recording | MEDIUM | PRJ-203 | Draft |

---

## REQ-WIM-001: Real-time stock sync between ERP and WMS

**Source**: Workshop 2026-03-05, WMS Integration Design Document
**Importance**: HIGH
**Platform Fit**: Custom Logic (API integration required)

### Context
Why this requirement exists: Warehouse teams currently rely on end-of-day batch updates for stock levels, causing discrepancies between the ERP and actual warehouse stock. This leads to overselling, incorrect purchase orders, and manual reconciliation work.
**Baseline**: The ERP has a standard inventory module with stock-on-hand tracking. However, it does not have a native real-time sync capability with the WMS. Custom API integration logic is required.
**Dependencies**: PRJ-102 (Order Processing) depends on accurate stock data from this requirement for availability checks.

### User Story
As a warehouse manager, I want real-time stock level synchronization between the ERP and WMS, so that all teams work with accurate inventory data and avoid overselling.

### Description
The system must synchronize stock levels between the ERP and WMS in near real-time (within acceptable latency). This includes inbound receipts, outbound shipments, adjustments, and cycle count results.

### Scenarios
- **Scenario 1**: Inbound receipt updates stock in both systems
  - *Origin*: Explicit (Workshop notes) — "When goods are received at the dock, the WMS confirms receipt and the ERP stock should update within minutes, not hours"
  - Given a purchase order PO-1234 for 500 units of Product A,
  - When the warehouse confirms receipt of 500 units in the WMS,
  - Then the ERP stock-on-hand for Product A increases by 500 within the acceptable latency threshold.

- **Scenario 2**: Outbound shipment reduces stock in both systems
  - *Origin*: Explicit (Transcript) — Maria Torres: "The picking confirmation in the WMS must immediately reduce ERP stock to prevent double-allocation"
  - Given a sales order SO-5678 for 200 units of Product B,
  - When the warehouse completes picking and confirms shipment in the WMS,
  - Then the ERP stock-on-hand for Product B decreases by 200 within the acceptable latency threshold.

- **Scenario 3**: Stock adjustment triggers sync
  - *Origin*: Inferred — implied by discussion on cycle count discrepancies
  - Given a cycle count reveals 50 units less than the system shows,
  - When the warehouse posts an adjustment in the WMS,
  - Then the ERP stock is adjusted accordingly and the discrepancy is logged.

### Acceptance Criteria
- [ ] Stock changes in WMS are reflected in ERP within the agreed latency threshold
- [ ] Stock changes in ERP are reflected in WMS within the agreed latency threshold
- [ ] Failed sync attempts are logged and trigger an alert to the integration team
- [ ] A reconciliation report can be generated showing sync status per product per location

### Open Questions
- [Q1]: What is the acceptable latency for stock sync? — Raised by: Alex Chen — Date: 2026-03-10
  *Context*: Real-time (sub-second) sync requires persistent connections and is more expensive. Near-real-time (1-5 minutes) may be sufficient for most operations but could cause issues during peak hours.

### Decisions
- [D1]: Use API-based real-time sync, not batch file transfer — Made by: James Park — Date: 2026-03-01
  *Context*: Batch processing (current state) causes 4-8 hour delays. API-based sync provides near-real-time updates. Alternative was message queue (Kafka) — considered over-engineered for current scale.

### Stakeholders
- Lead Business Owner: Maria Torres
- Consulted: James Park (architecture), Li Wei (WMS API)

---

## REQ-WIM-002: Lot tracking and traceability

**Source**: Regulatory requirements, Workshop 2026-03-05
**Importance**: HIGH
**Platform Fit**: Config (lot tracking exists in ERP, needs activation and configuration)

### Context
Why this requirement exists: Food safety regulations require full traceability of products from receipt to dispatch. Current lot tracking is manual (Excel) and does not integrate with the ERP.
**Baseline**: The ERP has a lot tracking module that can be activated. Lot number assignment and tracking through inventory transactions is supported OOTB. Custom configuration needed for specific attributes and GS1 compliance.
**Dependencies**: Linked to REQ-WIM-001 (stock sync must include lot-level data).

### User Story
As a quality manager, I want lot numbers tracked at every inventory transaction, so that I can trace any product from receipt to customer delivery within minutes during a recall.

### Description
The system must assign and track lot numbers for all inventory movements — receipt, storage, transfer, and dispatch. Lot attributes must include expiry date, origin, and quality grade.

### Scenarios
- **Scenario 1**: Lot assignment on receipt
  - *Origin*: Explicit (Workshop notes) — "Every inbound shipment gets a lot number at the dock. The lot follows the product through the warehouse."
  - Given a shipment of Product C arrives at the warehouse,
  - When the warehouse registers the receipt in the WMS,
  - Then a lot number is assigned following GS1 standards and recorded in both WMS and ERP.

- **Scenario 2**: Lot traceability query
  - *Origin*: Explicit (Transcript) — Maria Torres: "If there's a recall, I need to know within 30 minutes which customers received product from a specific lot"
  - Given lot L-2026-0042 has been partially shipped to 3 customers,
  - When a quality issue is reported for this lot,
  - Then the system can identify all customers who received product from this lot within 30 minutes.

### Acceptance Criteria
- [ ] Every inventory receipt generates a lot number following GS1 standards
- [ ] Lot numbers are tracked through all inventory transactions (receipt, transfer, pick, ship)
- [ ] A traceability report can identify all downstream recipients of a given lot within 30 minutes

### Stakeholders
- Lead Business Owner: Maria Torres
- Consulted: Quality team, Li Wei (WMS configuration)

---

## REQ-WIM-003: Location transfer recording

**Source**: Workshop 2026-03-05
**Importance**: MEDIUM
**Platform Fit**: Config (location transfers supported in ERP, approval workflow needs custom logic)

### Context
Why this requirement exists: Products are frequently moved between warehouse locations (e.g., from bulk storage to picking area). These transfers are not recorded, causing stock count discrepancies.
**Baseline**: The ERP supports location-based inventory. Transfer transactions between locations are supported OOTB. Custom logic needed for supervisor approval above a configurable threshold.
**Dependencies**: None identified.

### User Story
As a warehouse supervisor, I want all location transfers recorded in the system with approval controls, so that we maintain accurate stock counts per location.

### Description
The system must record every physical movement of inventory between warehouse locations. Transfers above a configurable quantity threshold require supervisor approval before execution.

### Scenarios
- **Scenario 1**: Standard transfer (below threshold)
  - *Origin*: Explicit (Workshop notes)
  - Given warehouse operator moves 50 units of Product D from location A1 to B3,
  - When the operator records the transfer in the WMS,
  - Then the stock is updated in both WMS and ERP (A1: -50, B3: +50) without requiring approval.

- **Scenario 2**: High-value transfer (above threshold)
  - *Origin*: Explicit (Transcript) — Maria Torres: "If someone moves more than 200 units at once, a supervisor must approve it. We've had mistakes before."
  - Given warehouse operator attempts to move 500 units of Product E from location A1 to C2,
  - When the quantity exceeds the configured approval threshold (200 units),
  - Then the transfer is held pending supervisor approval before execution.

### Acceptance Criteria
- [ ] All location transfers are recorded with timestamp, operator, source location, destination location, and quantity
- [ ] Transfers above the configurable threshold require supervisor approval
- [ ] Approved and rejected transfers are logged with the approver's name and timestamp

### Stakeholders
- Lead Business Owner: Maria Torres
- Consulted: Warehouse team leads
