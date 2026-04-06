# AGENTS.md — Project

This file is read by all coding agents at the start of every session. Follow every instruction here without exception.

---

## What You Are Building

This repository follows a spec-driven workflow for building a production-grade software project. Product details must be defined in the spec files.

Every session must produce work that is:

- Correct for current requirements
- Explicit about unknowns
- Robust to ambiguous prompts
- Safe from assumption drift

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

If any required file is missing, do not guess. Create or request the missing file explicitly before implementation.

---

## Prompt Intake Modes

Use one of these two modes at session start.

### Mode A — Detailed Prompt

Use when the user provides clear scope, constraints, and acceptance criteria.

Required behavior:

- Restate scope in 3-6 concrete bullets
- Identify what is already decidable vs still ambiguous
- Execute directly without unnecessary questioning
- Ask only blocking questions

### Mode B — Vague Prompt

Use when request lacks one or more of: goal, audience, scope, constraints, acceptance criteria.

Required behavior:

- Do a fast gap analysis before proposing implementation
- Ask a compact blocking-question set (max 7) grouped by theme
- Provide sensible defaults labeled as defaults, not facts
- Do not start irreversible implementation before blocking gaps are resolved

---

## Mandatory Clarification Gates

Before implementation, ensure all gates below are resolved or explicitly defaulted:

1. Outcome: what success looks like
2. Scope: what is in and out
3. Users: primary audience and key journeys
4. Constraints: performance, accessibility, SEO, legal/security, timeline
5. Content/data: source of truth and ownership
6. Integrations: required and forbidden external services
7. Acceptance criteria: objective pass/fail checks

If any gate is unresolved, write assumptions in session notes and mark confidence (`high`, `medium`, `low`).

---

## Assumption & Hallucination Policy

- Never present assumptions as confirmed facts
- Label assumptions with `Assumption:` and state verification path
- Never invent APIs, compatibility claims, or product constraints
- If uncertain about third-party behavior, verify from docs before coding
- If verification is not possible in-session, stop and flag uncertainty explicitly

Use this confidence rubric for important claims:

- `high`: confirmed by current source/spec or direct verification
- `medium`: inferred from strong pattern, not yet verified
- `low`: plausible but unverified, requires user decision or research

---

## Website Discovery Checklist (When Building a Website)

If project includes a website, gather or confirm:

1. Website type: marketing, app shell, docs, commerce, portal, or mixed
2. Top 3 user journeys
3. Information architecture (main pages/routes)
4. Content model and update ownership
5. SEO requirements (indexing, metadata, sitemap, structured data)
6. Accessibility target (minimum WCAG level)
7. Performance targets (for example LCP/CLS budgets)
8. Analytics and consent requirements
9. Internationalization/RTL requirements
10. Launch definition of done

If unknown, ask. If still unknown, apply defaults and log them in `spec/core/constraints.md` or session notes.

---

## How to Write Back After Every Session

Write:

1. Current session `executor.md`
2. `spec/status/progress.md`
3. `spec/status/next-session.md`
4. Append to `spec/core/decisions.md` for major decisions
5. Append to `spec/core/constraints.md` for new edge cases

Also include:

- Explicit assumptions made (if any)
- What was verified vs deferred
- Any unresolved decisions that may change implementation

### executor.md Format

```markdown
## Session: [YYYY-MM-DD-NNN-topic]

**Date:** YYYY-MM-DD
**Status:** completed | partial | blocked

### What Was Done

- [Concrete implementation changes]

### Decisions Made

- [Decision + rationale]

### Assumptions Used

- [Assumption + confidence + verification status]
- If none: "None"

### Deviations from Plan

- [Any deviation and why]
- If none: "None — executed as planned"

### Edge Cases Discovered

- [New edge cases]
- If none: "None"

### Verification Performed

- [What was verified: API behavior, compatibility, constraints]
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
- No hidden assumptions in implementation-critical logic

---

## Code Quality Standards

- TypeScript strict mode when TypeScript is used
- JSDoc for public functions and modules
- Explicit error handling
- Production-ready output
- Follow existing conventions before introducing new patterns
- Check `.agents/skills` first and follow relevant skills
- Every non-trivial implementation should include testable acceptance criteria

---

## Edge Case Obligations

Before implementing any task:

- What breaks if selected options conflict?
- What breaks at input boundaries?
- What breaks when dependency versions change?
- What breaks when requirements are interpreted differently?

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
- Continue implementation when blocking ambiguity is unresolved
