# decisions.md — Architectural Decisions Log

> Append-only. Never delete or edit past entries. Every significant architectural decision goes here with a date and rationale. Both planner and executor write here.

Format:

```
## [DATE] — [DECISION TITLE]
**Decision:** What was decided
**Rationale:** Why
**Alternatives considered:** What else was on the table
**Logged by:** planner | executor
```

---

## 2025-01-01 — Config-Driven Generation Over Pre-Baked Templates

**Decision:** All project generation is driven by a typed config object. There are no stored full templates per combination.
**Rationale:** The configuration matrix has too many dimensions for pre-baked templates to be maintainable. A config-driven approach scales to any combination without storing exponential template variants.
**Alternatives considered:** Storing one full template per framework (3 templates) with feature flags — rejected because it still leads to duplication and drift between templates.
**Logged by:** planner

## 2025-01-01 — UI Primitives Are Never User-Facing

**Decision:** Users never directly pick a UI primitive (Radix, Base UI, React Aria). The primitive is always resolved by the config based on which component library is selected.
**Rationale:** Primitives are an implementation detail of the component library. Exposing them to users creates confusion and invalid combinations. The config resolver owns this logic entirely.
**Alternatives considered:** Letting users pick primitive first, then component library — rejected because it allows invalid combinations and forces users to understand an architectural detail they shouldn't need to care about.
**Logged by:** planner

## 2025-01-01 — shadcn/ui Is the Only Library That Asks for Primitive

**Decision:** When the user selects shadcn/ui, they are then asked to choose Radix UI or Base UI. Coss UI locks to Base UI silently. HeroUI locks to React Aria silently.
**Rationale:** shadcn/ui genuinely supports both Radix and Base UI. The others do not — their primitive is fixed by their architecture. Asking the question only when there is a real choice avoids false optionality.
**Logged by:** planner

## 2025-01-01 — RTL Is a Shared Utility with Per-Library Adapters

**Decision:** RTL logic lives in a shared utility that exposes a single API, but generates library-specific bootstrap code under the hood (shadcn class transforms, Base UI DirectionProvider, React Aria I18nProvider).
**Rationale:** Each library has a different RTL contract. Duplicating RTL setup per template would cause drift and maintenance burden. A single utility with adapters keeps the interface consistent while respecting each library's requirements.
**Logged by:** planner

## 2025-01-01 — /spec Folder as Shared Agent Memory via Git

**Decision:** All planning and execution state is stored in `/spec` and exchanged via git. Perplexity (planner) writes plans and handoffs; Codex/other agents (executor) write execution notes back. Each session has its own folder.
**Rationale:** No direct communication channel between agents. Git is the shared medium. Session folders prevent unbounded file growth and token burn. `/spec/core/` files are stable reference material read every session.
**Logged by:** planner
