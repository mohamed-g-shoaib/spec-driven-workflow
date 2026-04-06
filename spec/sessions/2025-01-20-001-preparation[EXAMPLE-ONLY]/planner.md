# planner.md — Session 2025-01-20-001-preparation

**Date:** 2025-01-20
**Session type:** Planning
**Planned by:** Perplexity (planning agent)
**Status:** Handed off to executor

---

## Context Read

The following spec files were read before this session:

- `spec/core/context.md` ✓
- `spec/core/config-matrix.md` ✓
- `spec/core/stack.md` ✓
- `spec/core/constraints.md` ✓
- `spec/core/decisions.md` ✓ (initial entries only, no prior sessions)
- `spec/status/progress.md` ✓
- `spec/status/next-session.md` ✓
- No prior executor.md exists (this is session 001)

---

## Session Goal

Prepare the repository for development. This means establishing the monorepo structure, configuring TypeScript, setting up the OXC toolchain (Oxlint + Oxfmt), configuring git, and creating the skeleton folders for the CLI package and the marketing website — without writing any feature code yet.

This session produces no user-facing functionality. Its output is a clean, correctly configured foundation that all future sessions build on.

---

## Research & Decisions Made This Session

### 1. Monorepo vs Single Package

**Question:** Should Forge be a monorepo from the start, given it has two distinct parts (CLI tool + marketing website)?

**Decision:** Yes, monorepo from day one using `pnpm workspaces`.

**Rationale:**

- CLI and marketing website are separate deployable units with different dependencies
- Shared utilities (types, constants) can live in a `packages/shared` workspace
- Starting flat and migrating later costs more than starting with workspaces now
- pnpm workspaces has zero overhead for a project this size and avoids npm/yarn workspace quirks
- Turborepo considered but rejected for session 001 — it adds complexity before there's anything to pipeline. Can be added in a later session if build times become a concern.

**Alternatives considered:**

- Single package with subdirectories — rejected: no clean separation of concerns, shared node_modules causes confusion
- Nx — rejected: too much config overhead for current stage
- Turborepo — deferred, not rejected. Revisit when the project has multiple buildable packages.

### 2. TypeScript Configuration Strategy

**Decision:** One root `tsconfig.base.json` with strict settings. Each workspace extends it.

**Rationale:**

- Ensures strict mode is inherited everywhere — no workspace can accidentally relax it
- Single place to update compiler options that affect all packages
- Standard pattern for pnpm workspaces + TypeScript projects

**Key strict settings to enforce in base config:**

- `strict: true`
- `noUncheckedIndexedAccess: true`
- `exactOptionalPropertyTypes: true`
- `noImplicitReturns: true`
- `noFallthroughCasesInSwitch: true`

### 3. OXC Toolchain Setup

**Decision:** Oxlint for linting, Oxfmt for formatting, configured at root level to cover all workspaces.

**Research finding:** Oxlint does not require a Next.js-specific plugin for basic linting. It works with `.ts`, `.tsx` files natively. However, Next.js-specific rules (like no `<img>` tag enforcement) are not yet in Oxlint — this is an acceptable gap for now since Next.js's own build warnings cover most of these. Flag this in constraints.md.

**Config files needed:**

- `.oxlintrc.json` at root
- `oxfmt.toml` at root (or per-package if needed later)

### 4. Package Manager Lock

**Decision:** pnpm only for the Forge repository itself. The CLI tool must support generating projects for npm, pnpm, yarn, and bun — but the Forge monorepo uses pnpm.

**Rationale:** pnpm workspaces are the cleanest for monorepo management. Having a `pnpm-lock.yaml` at root prevents contributors from accidentally using npm or yarn and creating lock file conflicts.

### 5. Node.js Version

**Decision:** Node.js >= 20.0.0 (LTS). Enforced via `.nvmrc` and `engines` field in package.json.

**Rationale:** Node 20 is the current LTS. TanStack Start and Next.js 15 both require Node 18+. Setting 20 gives a margin while staying on a supported LTS line.

---

## Folder Structure Decided

```
forge/                          ← repo root
  packages/
    cli/                        ← the CLI tool (npx forge)
      src/
      package.json
      tsconfig.json
    web/                        ← marketing website (Next.js)
      src/
      package.json
      tsconfig.json
    shared/                     ← shared types, constants, utilities
      src/
      package.json
      tsconfig.json
  spec/                         ← agent shared memory (this folder)
  .oxlintrc.json                ← root linting config
  oxfmt.toml                    ← root formatting config
  tsconfig.base.json            ← base TypeScript config
  pnpm-workspace.yaml           ← workspace definition
  package.json                  ← root package.json (private: true)
  .nvmrc                        ← node version
  .gitignore
  AGENTS.md
  README.md
```

---

## Edge Cases Identified This Session

1. **pnpm version mismatch:** Contributors may have different pnpm versions. Enforce via `packageManager` field in root `package.json` (e.g. `"packageManager": "pnpm@9.x.x"`).

2. **Oxfmt config scope:** Oxfmt currently has limited configuration options compared to Prettier. If a formatting rule is needed that Oxfmt doesn't support, document it as a known gap rather than adding Prettier alongside it — two formatters is an anti-pattern we defined in constraints.md.

3. **shared package circular imports:** `packages/cli` and `packages/web` may both import from `packages/shared`, but `packages/shared` must never import from `cli` or `web`. Enforce this with Oxlint's import rules once configured.

4. **tsconfig paths across workspaces:** Path aliases (`@forge/shared`) must be configured in both `tsconfig.base.json` and the workspace `tsconfig.json` files, and must be kept in sync. Drift between these causes silent type errors.

---

## What Was NOT Decided (Deferred)

- CLI framework (clack vs enquirer) — deferred to session 002
- i18n library — deferred to session 003
- Auth × framework compatibility matrix — deferred to session 004
- Turborepo / build pipeline — deferred until multiple packages need coordinated builds
- CI/CD pipeline — deferred until there is something to build and test

---

## Notes for Executor

- Do not install any feature dependencies in this session. Only tooling and workspace plumbing.
- Do not scaffold the Next.js app yet — just create the folder structure and package.json files.
- Do not write any CLI logic — just the skeleton `src/index.ts` entry point with a placeholder.
- The goal is: `pnpm install` works, TypeScript compiles with zero errors, Oxlint runs clean, Oxfmt runs clean. That is the definition of done for this session.
- If Oxfmt doesn't support a rule we need, document it and move on — do not add Prettier.
