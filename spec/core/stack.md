# stack.md — Confirmed Tech Stack

> Update this file only when a technology choice is confirmed and locked. Do not add speculative or under-consideration entries here — those belong in session planner notes.

---

## CLI Tool

| Concern                 | Choice                  | Notes                                         |
| ----------------------- | ----------------------- | --------------------------------------------- |
| Runtime                 | Node.js                 | —                                             |
| Language                | TypeScript (strict)     | Always strict mode, no `any`                  |
| CLI framework           | TBD                     | Research required: clack, enquirer, or custom |
| Package manager support | npm · pnpm · yarn · bun | All four must work                            |
| Distribution            | npm package             | `npx forge` / `pnpm dlx forge` / `bunx forge` |

## Generated Project

| Concern             | Options                         | Notes                                            |
| ------------------- | ------------------------------- | ------------------------------------------------ |
| Frameworks          | Next.js · Vite · TanStack Start | See config-matrix.md                             |
| Styling             | Tailwind CSS v4                 | Required for HeroUI; confirmed for all           |
| Component libraries | shadcn/ui · Coss UI · HeroUI    | See config-matrix.md                             |
| Testing             | Playwright · Vitest             | Scaffolded per template                          |
| i18n library        | TBD                             | Research required: next-intl, i18next, Paraglide |

## Marketing Website

| Concern            | Choice                         | Notes                                                           |
| ------------------ | ------------------------------ | --------------------------------------------------------------- |
| Framework          | Next.js (App Router)           | Vercel-native, SEO-critical, RSC+ISR model                      |
| Deployment         | Vercel                         | Zero-config, ISR, image optimization out of the box             |
| UI library         | shadcn/ui                      | Primary target stack, best documented with Next.js              |
| Linting/Formatting | Oxlint + Oxfmt (OXC toolchain) | No conflict with Next.js                                        |
| UI sounds          | soundcn                        | shadcn CLI install, base64 TS modules, Web Audio API, zero deps |
| Styling            | Tailwind CSS v4                | Required by shadcn/ui new styles                                |

### soundcn Notes

- Install via shadcn CLI: `npx shadcn@latest add @soundcn/[sound-name]`
- Each sound is a self-contained `.ts` file with inline base64 data URI — no external assets
- Includes `useSound` hook for Web Audio API playback
- **Known browser constraint:** AudioContext is blocked until first user interaction — `play()` must only be called from direct user gesture handlers (click, keypress, pointer event). This is a browser policy, not a soundcn limitation.
- Bundle size: each sound adds ~10–100KB depending on duration. Import selectively — tree-shaking applies.
- The World of Warcraft sound pack is NOT CC0 — do not use it in any Forge-related project

## Branching Strategy

- `main` — stable, released
- `dev` — integration branch
- `feature/NNN-short-topic` — individual features, NNN matches session number
- Never commit directly to `main`
- All executor work on `feature/*` branches unless explicitly instructed otherwise

## Versioning

- Semantic versioning (semver)
- Changelog maintained at root `CHANGELOG.md`
