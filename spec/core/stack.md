# stack.md — Confirmed Tech Stack

> Update this file only when a technology choice is confirmed and 
> locked. Do not add speculative or under-consideration entries here 
> — those belong in session planner notes.

---

## [Layer 1 — e.g. CLI / Backend / API]
| Concern | Choice | Notes |
|---------|--------|-------|
| Language | TBD | |
| Runtime | TBD | |
| Framework | TBD | |

## [Layer 2 — e.g. Frontend / Website]
| Concern | Choice | Notes |
|---------|--------|-------|
| Framework | TBD | |
| Styling | TBD | |
| Deployment | TBD | |

## Branching Strategy
- `main` — stable, released
- `dev` — integration branch
- `feature/NNN-short-topic` — individual features
- Never commit directly to `main`
- All executor work on `feature/*` branches unless explicitly 
	instructed otherwise

## Versioning
- Semantic versioning (semver)
- Changelog maintained at root `CHANGELOG.md`
