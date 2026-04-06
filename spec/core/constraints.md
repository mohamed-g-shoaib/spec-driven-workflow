# constraints.md — Non-Negotiables, Anti-Patterns & Known Edge Cases

> Both planner and executor read this before every session. Executor appends new edge cases discovered during implementation. Never remove entries.

---

## Non-Negotiable Rules

1. **No framework logic in the core engine.** Framework-specific code lives in adapters only.
2. **No pre-baked full templates.** All generation is config-driven.
3. **No duplicated shared utilities.** RTL, i18n, and theming are injected once per project.
4. **No silent failures.** Every invalid config combination must be caught at resolution time, not at generation time, and never silently.
5. **No `any` in TypeScript.** Strict mode always. No exceptions.
6. **No TODOs in generated project code.** Users receive the output as-is.
7. **No assumptions about library APIs.** Verify against current version docs before implementing.
8. **No direct commits to `main`.** All work on feature branches.

---

## Known Invalid Config Combinations

| Combination                           | Reason                                         | Resolution                                                     |
| ------------------------------------- | ---------------------------------------------- | -------------------------------------------------------------- |
| Coss UI + Radix UI                    | Coss UI only supports Base UI                  | Config resolver rejects; surfaces error to user                |
| HeroUI + Radix UI or Base UI          | HeroUI only supports React Aria                | Config resolver rejects; surfaces error to user                |
| Biome + ESLint/Prettier               | Two linting stacks                             | Config resolver allows only one                                |
| Biome + Oxlint/Oxfmt                  | Two linting stacks                             | Config resolver allows only one                                |
| shadcn/ui + no primitive selected     | shadcn requires a primitive                    | Config resolver requires primitive choice when shadcn selected |
| Empty i18n array                      | At least one language required                 | Config resolver enforces minimum one language                  |
| Auth selected + unsupported framework | Not all auth adapters exist for all frameworks | Research per combination; resolver validates before generation |

---

## Known Edge Cases

### RTL

- shadcn/ui portal elements (Popover, Tooltip) require manual `dir` prop due to `tw-animate-css` known bug — must be scaffolded explicitly
- Base UI `DirectionProvider` does not affect HTML/CSS — `dir="rtl"` on HTML element must also be set separately; these are independent concerns
- React Aria direction is locale-driven — there is no standalone RTL toggle; enabling RTL without an RTL locale set will not behave as expected
- Manual RTL toggle (without Arabic in i18n) must still generate correct bootstrap for the selected library

### i18n

- AR triggers RTL automatically — but RTL bootstrap code differs per library (see config-matrix.md)
- Empty locale array must be caught before generation
- Language switcher in UI must reflect only the languages selected in config — not all supported languages

### Theming

- tweakcn theme API may change — theme pulling must be versioned or snapshotted, not live-fetched at user scaffold time
- Theme switching must not require a page reload — verify implementation approach per framework

### Auth

- Not all auth providers support all frameworks — this matrix is not fully researched; do not implement without verification
- Dashboard template + no auth selected is a valid but unusual combination — warn user, do not block

### Blank Template + UI Library

- A blank template with a component library selected still needs the library's provider setup scaffolded correctly
- RTL and theming utilities must still be injected if selected, even on a blank template

### Package Manager Detection

- Forge CLI must detect which package manager the user is using and use it for installs — do not hardcode npm
- `npx` / `pnpm dlx` / `bunx` / `yarn dlx` all need to work correctly
