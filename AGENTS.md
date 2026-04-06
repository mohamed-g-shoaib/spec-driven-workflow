# AGENTS.md — Project

This file is read by all coding agents at the start of every session. Follow every instruction here without exception.

---

## What You Are Building

This repository follows a spec-driven workflow for building a production-grade software project. Product details must be defined in the spec files.

---

## The /spec Folder — What It Is and How It Works

`/spec` is the shared memory between planning and execution agents. It is exchanged via git and is the source of truth.

### Folder Structure

```
/spec
  /core
    context.md
    config-matrix.md
    decisions.md
    stack.md
    constraints.md
  /sessions
    /YYYY-MM-DD-NNN-topic
      planner.md
      handoff.md
      executor.md
  /status
    progress.md
    next-session.md
```

### Purpose of Each File

| File                     | Who writes                | Who reads   | When it changes                                  |
| ------------------------ | ------------------------- | ----------- | ------------------------------------------------ |
| `core/context.md`        | Planner (initial setup)   | Both agents | Only on product vision change                    |
| `core/config-matrix.md`  | Planner                   | Both agents | When new config options are added                |
| `core/decisions.md`      | Both agents (append only) | Both agents | Every time a significant decision is made        |
| `core/stack.md`          | Both agents               | Both agents | When a technology choice is confirmed or changed |
| `core/constraints.md`    | Both agents (append only) | Both agents | When new edge cases or rules are discovered      |
| `sessions/*/planner.md`  | Planner                   | Executor    | Written once per session, never edited after     |
| `sessions/*/handoff.md`  | Planner                   | Executor    | Written once per session, never edited after     |
| `sessions/*/executor.md` | Executor                  | Planner     | Written by executor at end of session            |
| `status/progress.md`     | Executor                  | Both agents | End of every session                             |
| `status/next-session.md` | Planner or Executor       | Planner     | When a clear next topic emerges                  |

---

## How to Orient Yourself at the Start of Every Session

Read in this exact order before writing code:

1. `spec/core/context.md`
2. `spec/core/config-matrix.md`
3. `spec/core/stack.md`
4. `spec/core/constraints.md`
5. `spec/core/decisions.md` (last 5 entries minimum)
6. `spec/status/progress.md`
7. `spec/status/next-session.md`
8. Last 2 session folders in `spec/sessions/` (read `planner.md` and `executor.md`)
9. Current session folder (`handoff.md`)

---

## How to Write Back After Every Session

Write:

1. Current session `executor.md`
2. `spec/status/progress.md`
3. `spec/status/next-session.md`
4. Append to `spec/core/decisions.md` for major decisions
5. Append to `spec/core/constraints.md` for new edge cases

### executor.md Format

```markdown
## Session: [YYYY-MM-DD-NNN-topic]

**Date:** YYYY-MM-DD
**Status:** completed | partial | blocked

### What Was Done
- [Concrete implementation changes]

### Decisions Made
- [Decision + rationale]

### Deviations from Plan
- [Any deviation and why]
- If none: "None — executed as planned"

### Edge Cases Discovered
- [New edge cases]
- If none: "None"

### Blockers
- [Specific blockers]
- If none: "None"

### New Dependencies Introduced
- [Package, version, reason]
- If none: "None"

### Next Steps Suggested
- [What should be done next]
```

---

## Architecture Rules (Never Violate)

- Modular and composable design
- Framework-agnostic core where applicable
- Config-driven generation or behavior selection
- Extensible architecture (open for extension, closed for modification)
- Shared utilities for cross-cutting concerns
- No hardcoded combinations

---

## Code Quality Standards

- TypeScript strict mode when TypeScript is used
- JSDoc for public functions and modules
- Explicit error handling
- Production-ready output
- Follow existing conventions before introducing new patterns
- Check `.agents/skills` first and follow relevant skills

---

## Edge Case Obligations

Before implementing any task:

- What breaks if selected options conflict?
- What breaks at input boundaries?
- What breaks when dependency versions change?

If a new edge case is found, append it to `spec/core/constraints.md`.

---

## What You Must Never Do

- Put framework-specific logic into framework-agnostic core modules
- Hardcode option combinations
- Duplicate shared utilities
- Assume third-party API behavior without verifying
- Leave TODOs in shipped code
- Commit directly to `main`
- Skip required spec reads
