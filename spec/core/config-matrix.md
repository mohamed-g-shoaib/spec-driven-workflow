# config-matrix.md — Full Configuration Dimensions

> This is the canonical reference for every axis a user can configure. The config resolver must handle every combination described here. Both the planner and executor treat this as the source of truth.

---

## Framework

- Next.js
- Vite
- TanStack Start

## Language

- TypeScript _(default)_
- JavaScript

## Templates

Ordered from least to most complex:

- **Blank** — nothing, clean environment only
- **Blank + UI** — no page structure, but theme switcher and language switcher included
- **Simple Landing** — navbar, footer, theme switcher, language switcher
- **Rich Landing** — fully built landing page, more complex layout and sections
- **Landing + Auth** — rich landing page with authentication
- **Landing + Auth + Dashboard** — full product scaffold

## UI Primitives

> Never user-facing. Always resolved by the config based on component library selection.

| Primitive  | Resolved by                                |
| ---------- | ------------------------------------------ |
| Radix UI   | shadcn/ui (when user picks Radix)          |
| Base UI    | shadcn/ui (when user picks Base) · Coss UI |
| React Aria | HeroUI                                     |

If no component library is selected, no primitive is installed.

## UI Component Libraries

User picks one, or none.

| Library   | Primitive               | Notes                            |
| --------- | ----------------------- | -------------------------------- |
| shadcn/ui | Radix UI **or** Base UI | User is asked which primitive    |
| Coss UI   | Base UI                 | Primitive locked, not asked      |
| HeroUI    | React Aria              | Primitive locked, not asked      |
| None      | None                    | Raw primitive also not installed |

**Conflict rule:** Selecting Coss UI + Radix UI is invalid. Selecting HeroUI + any non-React Aria primitive is invalid. Config resolver must catch and reject these before generation.

## Linting & Formatting

User picks one:

- ESLint + Prettier
- Biome
- Oxlint + Oxfmt

**Conflict rule:** Only one linting/formatting stack can be selected. Selecting more than one is invalid.

## Internationalization (i18n)

Any combination of:

- EN (English)
- AR (Arabic)
- ES (Spanish)
- FR (French)

Extensible — new locales can be added without restructuring. At least one language must be selected.

## RTL (Right-to-Left)

- Auto-triggered when Arabic or any other RTL language is included in i18n selection
- Can also be manually enabled independent of language selection
- Each component library has a different RTL contract:

### shadcn/ui RTL

- Configured via `rtl: true` in `components.json`
- CLI automatically transforms physical classes to logical equivalents (`left-*` → `start-*`)
- Animation classes are also transformed (`slide-in-from-right` → `slide-in-from-end`)
- **Known issue:** Portal elements (Popover, Tooltip) require manual `dir` prop due to `tw-animate-css` bug — must be scaffolded correctly, not left to the user

### Base UI / Coss UI RTL

- Requires `<DirectionProvider direction="rtl">` wrapping components
- Also requires `dir="rtl"` on the HTML element separately
- These are two **independent concerns** — both must be set. Setting only one is a silent failure.
- `useDirection` hook available for portaled components rendered outside app root

### React Aria / HeroUI RTL

- Direction derived from `useLocale` hook
- Override via `<I18nProvider locale="ar">` at app root
- Direction is **locale-driven**, not a standalone toggle
- BCP47 locale string controls both language and direction

**Shared direction utility:** The config resolver must generate library-specific RTL bootstrap code, but expose a single consistent API to the generated project. The utility must abstract over all three mechanisms.

## Authentication

User picks one, or none:

- None
- Better Auth
- NextAuth / Auth.js
- Supabase Auth

**Compatibility note:** Not all auth providers have adapters for all frameworks. Config resolver must validate auth + framework combination before generation. Research required before adding new combinations.

## Theming

- Light/Dark mode toggle (always included when a component library is selected)
- Multiple named themes pulled from tweakcn
- Managed by a shared theme utility injected at generation time
- Theme switching must not require a page reload

## Git

- Initialize a git repository: yes or no
