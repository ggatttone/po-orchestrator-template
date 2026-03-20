# Epic: Warehouse Inventory Management

## Epic Key
PRJ-101

## Status
Discovery

## Objective
Implement real-time inventory visibility across 3 warehouse locations by integrating the ERP system with the WMS, enabling accurate stock levels, lot tracking, and location transfer recording.

## Scope

### In scope
- Real-time stock level synchronization between ERP and WMS
- Lot tracking and traceability (inbound, storage, outbound)
- Location transfer recording and approval workflow
- Stock discrepancy detection and alerting

### Out of scope
- Warehouse automation (robotics, conveyor systems)
- Picking route optimization
- Barcode scanner hardware procurement
- WMS vendor selection (already completed)

## Key stakeholders
| Name | Role | Responsibility |
|---|---|---|
| Alex Chen | Product Owner | Backlog prioritization, story acceptance |
| Maria Torres | Business Owner — Warehouse Ops | Process decisions, requirement approval |
| James Park | Solution Architect | Technical design, integration architecture |
| Li Wei | WMS Consultant | WMS configuration, API specifications |

## Related epics
- PRJ-102 (Order Processing) — depends on stock availability data from this epic
- PRJ-103 (Transport Planning) — uses location data for dispatch planning

## Current sprint focus
Mapping current WMS API endpoints and gathering latency requirements from Operations team

## Development block
DB-3 (Apr-May 2026)

## Last updated
2026-03-20