# Architecture Decision Records (ADR)

## ADR-001: Use orchestrator + sub-agent architecture

**Date**: [Your start date]
**Status**: Accepted

**Context**: A Product Owner needs AI support across many different task types (requirements, stories, planning, communications). A single monolithic prompt would be too large, unfocused, and hard to maintain.

**Decision**: Use an orchestrator pattern where `CLAUDE.md` routes tasks to 8 specialized sub-agents, each with a focused scope and clear rules.

**Motivation**: Separation of concerns. Each agent can be optimized independently. New agents can be added without changing existing ones. The orchestrator acts as a routing layer, not an executor.

**Alternatives considered**: Single large prompt (rejected: too large, hard to maintain), separate Claude Code projects per task (rejected: loses cross-epic context).

---

## ADR-002: Epic-as-directory pattern for context isolation

**Date**: [Your start date]
**Status**: Accepted

**Context**: Managing multiple parallel epics requires context isolation — working on one epic should not pollute another. But agents also need to scan across epics for dependencies.

**Decision**: Each epic gets its own directory under `epics/` with 7 standard files. Agents read the current epic's directory for focus, and can scan other epics for cross-references.

**Motivation**: Physical separation (directories) provides natural context boundaries. Standard file structure enables automated scanning and consistency checks.

**Alternatives considered**: Single requirements database (rejected: no isolation), separate git branches per epic (rejected: merge complexity).

---

## ADR-003: Knowledge base outside .claude/ for shared access

**Date**: [Your start date]
**Status**: Accepted

**Context**: Reference files (glossary, standards, governance) need to be accessible by all tools — not just Claude Code agents. If stored inside `.claude/`, they would be invisible to other editors and AI tools.

**Decision**: Place all shared reference files in `knowledge/` at the project root, outside `.claude/`.

**Motivation**: Shared access for Claude Code agents, Codex, manual editing, and any future tools. The `knowledge/` directory is a first-class part of the project, not hidden infrastructure.

**Alternatives considered**: Inside `.claude/` (rejected: limited access), inside `docs/` (rejected: docs are project documentation, not operational reference).

---

<!-- Add new ADRs below following this format:

## ADR-NNN: [Title]

**Date**: [Date]
**Status**: [Proposed / Accepted / Deprecated / Superseded]

**Context**: [What prompted this decision]
**Decision**: [What was decided]
**Motivation**: [Why this approach was chosen]
**Alternatives considered**: [What else was evaluated]
-->
