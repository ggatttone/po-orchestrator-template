# Way of Working — Agile Delivery Methodology

<!-- CUSTOMIZE: Adjust sprint cadence, stagegate definitions, and DoR/DoD to match your delivery methodology -->

## Sprint Structure

- **Sprint duration**: 2 weeks (adjust to your cadence)
- **Sprint ceremonies**: Planning, Daily Standup, Review, Retrospective
- **Refinement**: Separate from sprint planning, held 1-2x per sprint

## Development Blocks

Development Blocks (DB) group epics into delivery milestones. Each DB typically spans 2-3 sprints.

| Block | Duration | Focus |
|---|---|---|
| DB-1 | [dates] | [description] |
| DB-2 | [dates] | [description] |
| DB-3 | [dates] | [description] |

## Stagegates

Stagegates are quality gates between development phases:

| Stagegate | Purpose | Entry criteria |
|---|---|---|
| SG1 | Scope approval | Epic objective, in/out scope, key stakeholders defined |
| SG2 | Design approval | Requirements documented, baseline analysis complete, dependencies mapped |
| SG3 | Build readiness | Stories meet DoR, test scenarios outlined, no blocking questions |
| SG4 | Go-live readiness | All ACs passed, UAT complete, training materials ready |

## Definition of Ready (DoR)

A story is "Ready" for sprint when ALL 5 criteria are met:

1. Description and acceptance criteria are clear
2. Dependencies are identified and resolved (or planned)
3. Story is estimated
4. Test scenarios are outlined
5. No blocking open questions remain

## Definition of Done (DoD)

A story is "Done" when:

1. All acceptance criteria are met and verified
2. Code reviewed and merged
3. Tested in the integration environment
4. Documentation updated (if applicable)
5. Product Owner has accepted the deliverable
