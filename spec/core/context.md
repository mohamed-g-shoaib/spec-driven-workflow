# context.md — Forge Project Overview

> This file is the permanent source of truth for what Forge is. It changes only when the product vision changes. Do not append session notes here.

---

## What Is Forge?

Forge is a CLI scaffolding tool that generates fully configured, production-ready projects. Users define their preferences either through a marketing website (visual config builder that outputs a CLI command) or directly through interactive CLI prompts. The output is a complete, working project — not a starter that needs more setup.

Forge is similar to `create-next-app`, Vite's scaffolding, and the T3 Stack, but is more modular, more variety-rich, and opinionated only where it adds value.

## Who Builds It

A front-end developer with hands-on experience in Next.js, Tailwind CSS, shadcn/ui, RTL support, Playwright, and Vitest.

## Who Uses It

Frontend and full-stack developers who want a fast, high-quality project start without making every setup decision from scratch. Expected scale: 10,000+ users.

## Two Entry Points

1. **Marketing website** — users browse templates, configure options visually, and receive a ready-to-copy CLI command
2. **CLI tool** — users run the command and answer prompts interactively in the terminal

Both entry points resolve to the same config object that drives code generation.

## Core Philosophy

- **Config-driven, not template-driven** — there are no pre-baked full templates. A typed config object drives all generation.
- **Modular and composable** — every feature is an independent layer. Nothing bleeds into another.
- **Extensible by design** — adding new options must never require restructuring existing code.
- **Production-grade output** — generated code is ready for real use, not demo quality.
- **Shared utilities** — RTL, i18n, and theming are injected utilities, never duplicated.
