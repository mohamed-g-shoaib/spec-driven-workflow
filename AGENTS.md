# AGENTS.md — Forge

This file is read by all coding agents (Codex, Claude Code, Cursor, etc.) at the start of every session. Follow every instruction here without exception.

---

## What You Are Building

**Forge** is a CLI scaffolding tool that generates fully configured, production-ready projects based on a user-defined config object. It is similar to `create-next-app` and T3 Stack but more modular and variety-rich. It serves 10,000+ users. Every line of code you write must reflect that.

---

## The /spec Folder — What It Is and How It Works

`/spec` is the shared memory between the planning agent (Perplexity) and the execution agent (you). It is exchanged via git. You read from it to orient yourself. You write back to it after every session. It is the only source of truth.

### Folder Structure

```
/spec
  /core                  ← Permanent reference. Read every session. Never grows large.
    context.md           ← What Forge is. Changes only if the product vision changes.
    config-matrix.md     ← Every configuration dimension and its rules. Source of truth.
    decisions.md         ← Append-only log of every architectural decision made.
    stack.md             ← Confirmed technology choices and versions.
    constraints.md       ← Non-negotiables, invalid combinations, known edge cases.
  /sessions              ← One folder per session. Never edit past sessions.
    /YYYY-MM-DD-NNN-topic
      planner.md         ← Written by Perplexity. The plan, research, questions.
      handoff.md         ← Written by Perplexity. Your specific task instructions.
      executor.md        ← Written by YOU. What you did, decisions, deviations, blockers.
  /status                ← Live state. Updated every session.
    progress.md          ← What is done, in progress, and blocked.
    next-session.md      ← Seed for the next planning session. Written by you or planner.
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
| `sessions/*/executor.md` | Executor (you)            | Planner     | Written by you at end of session                 |
| `status/progress.md`     | Executor (you)            | Both agents | End of every session                             |
| `status/next-session.md` | Either agent              | Planner     | When a clear next topic emerges                  |

---

## How to Orient Yourself at the Start of Every Session

Read in this exact order before writing a single line of code:

1. `spec/core/context.md`
2. `spec/core/config-matrix.md`
3. `spec/core/stack.md`
4. `spec/core/constraints.md`
5. `spec/core/decisions.md` — read the last 5 entries minimum
6. `spec/status/progress.md`
7. `spec/status/next-session.md`
8. The last 2 session folders in `spec/sessions/` sorted by date — read `planner.md` and `executor.md` in each
9. The current session folder — read `handoff.md` for your specific instructions

Do not skip any of these. Do not assume you know the current state from prior context.

---

## How to Write Back After Every Session

At the end of every session, write the following before stopping:

### executor.md (in current session folder)

Use this exact format:

```markdown
## Session: [YYYY-MM-DD-NNN-topic]

**Date:** YYYY-MM-DD
**Status:** completed | partial | blocked

### What Was Done

- [Concrete list of what was implemented or changed]

### Decisions Made

- [Any architectural or implementation decision made during execution, with rationale]
- If significant, also log to core/decisions.md

### Deviations from Plan

- [Anything that differed from handoff.md, and why]
- If none: "None — executed as planned"

### Edge Cases Discovered

- [Any new edge cases found during implementation]
- Also append to core/constraints.md if non-trivial

### Blockers

- [Anything that stopped or slowed progress, with specifics]
- If none: "None"

### New Dependencies Introduced

- [Package name, version, reason]
- If none: "None"

### Next Steps Suggested

- [What should follow from this session]
```

### progress.md

Update the Done / In Progress / Blocked / Not Started sections to reflect current state.

### next-session.md

If this session reveals a clear next topic, overwrite with the seed for the next planning session.

### core/decisions.md

If you made an architectural decision not already logged, append it using the existing format.

### core/constraints.md

If you discovered a new edge case or invalid combination, append it.

---

## Output Format Standards

When writing to any spec file, follow these rules:

- **Be precise and lean.** Future agents read what you write. No padding, no summaries of obvious things.
- **Use the formats defined in each file.** Don't invent new sections.
- **Dates always in YYYY-MM-DD format.**
- **Decisions always include rationale.** A decision without a reason is useless to the next agent.
- **Blockers must be specific.** "It didn't work" is not a blocker. "Oxlint v0.x does not have a Next.js plugin — tracked at [link]" is a blocker.
- **Never delete past content** from decisions.md or constraints.md — append only.
- **Never edit past session folders** — they are immutable records.

---

## Architecture Rules (Never Violate)

- **Modular & composable** — every feature is an independent layer. No feature bleeds into another. If you find yourself coupling two concerns, stop and flag it in executor.md.
- **Framework-agnostic core** — the CLI engine and config resolver must never contain framework-specific logic. Framework logic lives in adapters only.
- **Config-driven generation** — user selections produce a typed config object. That config object drives all code generation. Never generate code based on implicit assumptions.
- **Extensible by design** — adding a new framework, auth provider, UI library, or language must never require restructuring existing code. Open/closed principle strictly enforced.
- **Shared utilities** — theme management, i18n, and RTL are shared utilities injected by the config resolver. Never duplicated per template or per framework.
- **No hardcoded combinations** — there are no pre-baked full templates. Everything is composed at generation time from the config.

---

## Code Quality Standards

- TypeScript strict mode always — no `any`, no implicit types
- Every public function and module must have a JSDoc comment
- Error handling must be explicit — no silent failures, no swallowed exceptions
- All generated project code must be production-ready — not demo quality
- Follow existing conventions in the codebase — check before inventing
- If a pattern already exists in the codebase, use it. Do not introduce a second way to do the same thing.
- Check `.agents/skills` for project-specific patterns before implementing anything. If a skill exists for the task, follow it. If none exists, note that one should be created and proceed with the most engineered approach available.

---

## Configuration Matrix (Know This Cold)

- **Frameworks:** Next.js · Vite · TanStack Start
- **Language:** TypeScript · JavaScript
- **UI Primitives (never user-facing, always config-resolved):** Radix UI · Base UI · React Aria
- **UI Component Libraries:** shadcn/ui (Radix or Base, user picks) · Coss UI (locks Base UI) · HeroUI (locks React Aria) · none
- **Linting/Formatting:** ESLint+Prettier · Biome · Oxlint+Oxfmt
- **i18n:** EN · AR · ES · FR (extensible)
- **RTL:** auto-triggered by RTL language selection, manually toggleable. Three different implementation contracts — see `spec/core/config-matrix.md` for full details
- **Auth:** None · Better Auth · NextAuth/Auth.js · Supabase Auth
- **Theming:** Light/Dark + named themes from tweakcn via shared theme utility
- **Git:** init or not
- **Marketing website:** Next.js (App Router) · shadcn/ui · Oxlint+Oxfmt · soundcn · deployed on Vercel — see `spec/core/stack.md`

Full rules and conflict resolution in `spec/core/config-matrix.md`.

---

## Edge Case Obligations

Before implementing any task:

- What breaks if two selected options conflict?
- What breaks at the boundary of valid input?
- What breaks when a dependency changes version?

If you find an edge case not already in `spec/core/constraints.md`, append it before proceeding. If an edge case would block correct implementation, document it in `executor.md` and flag it clearly rather than working around it silently.

---

## What You Must Never Do

- Write framework-specific logic in the core engine
- Hardcode any config combination — all generation is config-driven
- Duplicate shared utilities (RTL, i18n, theming) across templates
- Write code that assumes a specific UI library, auth provider, or framework without reading from the config object
- Introduce a new dependency without documenting the reason in `executor.md`
- Ignore the RTL/i18n axis when touching anything UI-related
- Leave TODOs in generated project code — users will receive it as-is
- Commit directly to `main` — follow the branching strategy in `spec/core/stack.md`
- Skip reading spec files and assume you already know the current state
