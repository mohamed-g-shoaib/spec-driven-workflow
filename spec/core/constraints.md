# constraints.md — Non-Negotiables, Anti-Patterns & Known Edge Cases

> Both planner and executor read this before every session. Executor 
> appends new edge cases discovered during implementation. Never 
> remove entries.

---

## Non-Negotiable Rules

1. **No silent failures** — every invalid state must be caught and 
	handled explicitly, never ignored.
2. **No assumptions** — verify library APIs, framework behavior, and 
	tool compatibility before implementing. Say so if uncertain.
3. **No direct commits to `main`** — all work on feature branches.
4. **No duplicate utilities** — anything used in more than one place 
	is a shared utility, never copied.
5. **No TODOs in shipped code** — users and consumers receive output 
	as-is.
6. [Add your project-specific non-negotiables here]

---

## Known Invalid Combinations

| Combination | Reason | Resolution |
|-------------|--------|------------|
| [Add as discovered] | | |

---

## Known Edge Cases

[Add as discovered during planning and execution]
