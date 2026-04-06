# next-session.md — Seed for Next Planning Session

> Written by executor at end of session, or by planner when deferring a topic. Planner reads this first to orient the next session.

---

## Recommended Focus for Next Session

**Research phase** — Before any code is written, the following decisions need to be researched and locked in `/spec/core/stack.md`:

1. **CLI framework** — Research and compare: `clack` (by Nate), `enquirer`, and rolling a minimal custom solution. Evaluate against: multi-step prompts, validation, cancellation handling, testability, maintenance health.

2. **i18n library** — Research and compare: `next-intl`, `i18next` + `react-i18next`, `Paraglide JS`. Evaluate against: framework support (Next.js, Vite, TanStack Start), RTL handling, bundle size, DX, server/client split for Next.js App Router.

3. **Auth × framework matrix** — For each auth provider (Better Auth, NextAuth/Auth.js, Supabase Auth), confirm which frameworks are supported with adapters today. Document gaps.

4. **Package manager detection** — Research how `create-next-app` and T3 detects the calling package manager. Confirm approach for Forge.

## Questions for Planner to Answer Before Research Begins

- Is there a preferred CLI framework already in mind?
- Should the marketing website be built before or after the CLI is functional?
- Is Tailwind CSS v4 confirmed as the only styling option, or will v3 also be supported?
