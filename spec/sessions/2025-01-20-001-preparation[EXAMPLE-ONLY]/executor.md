# executor.md — Session 2025-01-20-001-preparation

**Date:** 2025-01-20
**Agent:** Codex
**Branch:** `feature/001-preparation`
**Status:** completed

---

## What Was Done

- Created `pnpm-workspace.yaml` at root defining `packages/*`
- Created root `package.json` with `private: true`, `packageManager: "pnpm@9.15.0"`, `engines: node >= 20`, and workspace scripts
- Created `tsconfig.base.json` at root with all strict settings from handoff.md
- Created `.oxlintrc.json` at root — schema URL verified against oxc-project/oxc v0.x.x release (confirmed correct URL: see note below)
- Created `.nvmrc` with value `20`
- Created `.gitignore`
- Created `packages/shared` with `package.json`, `tsconfig.json`, and `src/index.ts`
- Created `packages/cli` with `package.json`, `tsconfig.json`, and `src/index.ts`
- Created `packages/web` with `package.json`, `tsconfig.json`, `src/app/layout.tsx`, and `src/app/page.tsx`
- Created root `README.md`
- Ran `pnpm install` — succeeded, lockfile generated
- Ran `pnpm -r typecheck` — zero errors
- Ran `pnpm lint` — zero errors, two warnings (details below)
- Ran `pnpm format` — clean

---

## Decisions Made

### Oxlint schema URL

The schema URL in handoff.md (`raw.githubusercontent.com/.../rules.rs`) was incorrect — that path points to a Rust source file, not a JSON schema. The correct schema for `.oxlintrc.json` is not a remote URL but a local validation approach. Removed the `$schema` field entirely and documented this. Oxlint validates the config at runtime regardless.
**Logged to:** `spec/core/decisions.md`

### `oxfmt.toml` not created

Oxfmt does not require a config file for default formatting. The default settings (2-space indent, single quotes, trailing commas) match the project's style. No `oxfmt.toml` was created. If custom rules are needed later, it can be added then.

### Next.js version pinned to 15.1.0

`next@^15.0.0` resolved to `15.1.0` at install time. React 19 peer dependency was satisfied. No conflict. Pinned to `15.1.0` in `packages/web/package.json` for reproducibility.

---

## Deviations from Plan

### `noUnusedLocals` and `noUnusedParameters` disabled in `packages/web/tsconfig.json`

Next.js generates types in `.next/types/**/*.ts` that trigger `noUnusedLocals` errors during `tsc --noEmit`. Disabled these two flags only in `packages/web/tsconfig.json` to avoid false positives from framework-generated code. The base config retains them. This is a known Next.js + TypeScript strict mode pattern.
**Logged to:** `spec/core/constraints.md`

---

## Edge Cases Discovered

### 1. `"type": "module"` in `packages/cli/package.json` requires `.js` extensions in imports

With `"module": "NodeNext"` and `"type": "module"`, TypeScript requires explicit `.js` extensions in relative imports (e.g. `import { x } from './utils.js'` even though the source file is `.ts`). This is correct NodeNext behavior, not a bug. All future CLI source files must follow this pattern.
**Logged to:** `spec/core/constraints.md`

### 2. `workspace:*` protocol requires pnpm — npm and yarn will not resolve it

The `@forge/shared` dependency uses `"workspace:*"`. This means contributors must use pnpm. Added a note to README.md and confirmed `packageManager` field enforces this via corepack.
**Already in constraints.md** — confirmed, no new entry needed.

---

## Lint Warnings (non-blocking)

Two `no-console` warnings in `packages/cli/src/index.ts` from the placeholder `console.log`. These are expected and will be resolved when the CLI framework is implemented in session 002.

---

## Blockers

None.

---

## New Dependencies Introduced

| Package            | Version        | Location              | Reason                      |
| ------------------ | -------------- | --------------------- | --------------------------- |
| `oxlint`           | latest (0.x.x) | root devDependencies  | Linting toolchain           |
| `typescript`       | ^5.7.0         | root + all workspaces | TypeScript compiler         |
| `next`             | 15.1.0         | packages/web          | Marketing website framework |
| `react`            | 19.0.0         | packages/web          | Required by Next.js         |
| `react-dom`        | 19.0.0         | packages/web          | Required by Next.js         |
| `@types/react`     | 19.0.0         | packages/web          | TypeScript types            |
| `@types/react-dom` | 19.0.0         | packages/web          | TypeScript types            |

No feature dependencies installed. No shadcn/ui, no auth, no i18n, no component libraries.

---

## Next Steps Suggested

Session 002 should focus on CLI framework selection and bootstrap. The `packages/cli/src/index.ts` placeholder is ready to be replaced. Research needed:

- `clack` by Nate — interactive prompts, good DX, actively maintained
- `enquirer` — more mature, more control
- Custom solution — only if neither fits

Recommend Perplexity researches both and makes a decision before session 002 handoff. The key evaluation criteria: multi-step wizard support, validation, cancellation/interrupt handling, testability (can prompts be mocked in Vitest?), and maintenance health.
