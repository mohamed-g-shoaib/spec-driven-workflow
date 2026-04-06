# handoff.md — Session 2025-01-20-001-preparation

**To:** Coding agent (Codex / Claude Code / Cursor)
**From:** Perplexity (planning agent)
**Date:** 2025-01-20
**Branch:** `feature/001-preparation`

---

## Your Task

Set up the Forge monorepo foundation. No feature code. No UI. No CLI logic. Just a clean, correctly configured base that compiles and lints without errors.

**Definition of done:**

- `pnpm install` runs successfully from root
- `pnpm typecheck` passes with zero errors across all workspaces
- `pnpm lint` runs Oxlint clean across all workspaces
- `pnpm format` runs Oxfmt clean across all workspaces
- Folder structure matches the spec below exactly

---

## Step-by-Step Instructions

### 1. Create the repo root files

**`pnpm-workspace.yaml`**

```yaml
packages:
  - "packages/*"
```

**Root `package.json`**

```json
{
  "name": "forge",
  "private": true,
  "version": "0.0.0",
  "packageManager": "pnpm@9.15.0",
  "engines": {
    "node": ">=20.0.0"
  },
  "scripts": {
    "typecheck": "pnpm -r typecheck",
    "lint": "oxlint .",
    "format": "oxfmt .",
    "build": "pnpm -r build"
  },
  "devDependencies": {
    "oxlint": "latest",
    "typescript": "^5.7.0"
  }
}
```

**`.nvmrc`**

```
20
```

**`.gitignore`**

```
node_modules/
dist/
.next/
.turbo/
*.tsbuildinfo
.env
.env.local
```

---

### 2. Create `tsconfig.base.json` at root

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022"],
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "skipLibCheck": true
  }
}
```

---

### 3. Create `.oxlintrc.json` at root

```json
{
  "$schema": "https://raw.githubusercontent.com/oxc-project/oxc/main/crates/oxc_linter/src/rules.rs",
  "rules": {
    "no-unused-vars": "error",
    "no-console": "warn",
    "eqeqeq": "error",
    "no-var": "error",
    "prefer-const": "error"
  },
  "ignore": ["node_modules", "dist", ".next"]
}
```

> Note: Verify the correct schema URL for the installed version of Oxlint before committing. Do not hardcode a URL you have not confirmed.

---

### 4. Create `packages/shared`

**`packages/shared/package.json`**

```json
{
  "name": "@forge/shared",
  "version": "0.0.0",
  "private": true,
  "type": "module",
  "main": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "typecheck": "tsc --noEmit"
  },
  "devDependencies": {
    "typescript": "^5.7.0"
  }
}
```

**`packages/shared/tsconfig.json`**

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src"]
}
```

**`packages/shared/src/index.ts`**

```typescript
// @forge/shared
// Shared types, constants, and utilities for the Forge monorepo.
// Add exports here as they are defined.

export const FORGE_VERSION = "0.0.0";
```

---

### 5. Create `packages/cli`

**`packages/cli/package.json`**

```json
{
  "name": "@forge/cli",
  "version": "0.0.0",
  "private": false,
  "type": "module",
  "bin": {
    "forge": "./dist/index.js"
  },
  "scripts": {
    "build": "tsc",
    "typecheck": "tsc --noEmit",
    "dev": "node --watch dist/index.js"
  },
  "dependencies": {
    "@forge/shared": "workspace:*"
  },
  "devDependencies": {
    "typescript": "^5.7.0"
  }
}
```

**`packages/cli/tsconfig.json`**

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src"]
}
```

**`packages/cli/src/index.ts`**

```typescript
/**
 * Forge CLI — entry point
 *
 * This file is the binary entry point for the `forge` CLI tool.
 * Feature implementation begins in session 002 (CLI framework selection).
 */

// TODO(session-002): Replace this placeholder with the CLI framework bootstrap.
console.log("Forge CLI — coming soon");
```

---

### 6. Create `packages/web`

**`packages/web/package.json`**

```json
{
  "name": "@forge/web",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "typecheck": "tsc --noEmit"
  },
  "dependencies": {
    "@forge/shared": "workspace:*",
    "next": "^15.0.0",
    "react": "^19.0.0",
    "react-dom": "^19.0.0"
  },
  "devDependencies": {
    "@types/react": "^19.0.0",
    "@types/react-dom": "^19.0.0",
    "typescript": "^5.7.0"
  }
}
```

**`packages/web/tsconfig.json`**

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "jsx": "preserve",
    "plugins": [{ "name": "next" }],
    "paths": {
      "@/*": ["./src/*"],
      "@forge/shared": ["../shared/src/index.ts"]
    }
  },
  "include": ["src", "next-env.d.ts", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

**`packages/web/src/app/layout.tsx`**

```typescript
/**
 * Root layout — Forge marketing website
 * Full implementation begins once shadcn/ui and theming are set up (future session).
 */
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

**`packages/web/src/app/page.tsx`**

```typescript
/**
 * Home page — placeholder
 * Full implementation begins in the marketing website session.
 */
export default function HomePage() {
  return (
    <main>
      <h1>Forge — coming soon</h1>
    </main>
  );
}
```

---

### 7. Create root `README.md`

```markdown
# Forge

A CLI scaffolding tool for generating fully configured, production-ready projects.

## Development

This repository uses pnpm workspaces. Node.js >= 20 required.

\`\`\`bash
pnpm install
pnpm typecheck
pnpm lint
\`\`\`

## Packages

| Package         | Description                |
| --------------- | -------------------------- |
| `@forge/cli`    | The CLI tool (`npx forge`) |
| `@forge/web`    | Marketing website          |
| `@forge/shared` | Shared types and utilities |

## Agent Workflow

See `AGENTS.md` for coding agent instructions.
See `spec/` for planning and execution history.
```

---

## Verification Checklist

Before writing executor.md, confirm each item:

- [ ] `pnpm install` runs from root with no errors
- [ ] `pnpm -r typecheck` passes with zero TypeScript errors
- [ ] `pnpm lint` runs Oxlint with no errors (warnings acceptable)
- [ ] `pnpm format` runs Oxfmt with no errors
- [ ] Folder structure matches the spec in planner.md exactly
- [ ] No feature dependencies installed (no shadcn, no auth libraries, no i18n)
- [ ] No `any` types anywhere
- [ ] No TODOs left untagged (all TODOs must follow format `// TODO(session-NNN): reason`)

---

## What To Do If Something Goes Wrong

**Oxlint schema URL is wrong:** Find the correct schema from the oxc-project GitHub releases page. Document the correct URL in executor.md. Do not guess.

**TypeScript path aliases not resolving:** Check that `paths` in the workspace `tsconfig.json` matches the actual file locations. Check that `moduleResolution` is consistent between base and workspace configs.

**pnpm workspace protocol not resolving:** Ensure `@forge/shared` is listed under `dependencies` as `"workspace:*"` and that `pnpm install` was run after adding it.

**Next.js version conflicts with React 19:** Check Next.js 15 peer dependency requirements. If there is a conflict, pin to the latest compatible versions and document in executor.md.
