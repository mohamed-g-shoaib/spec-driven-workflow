# Session Workflow Guide

This is your personal reference for running planning and execution sessions. Read this once and keep it handy.

Before your first session, configure your planning agent using the template in `docs/PLANNER-INSTRUCTIONS.md`. The generic template works for any project. The [YOUR PROJECT NAME] example shows a fully filled-in version.

---

## The Loop at a Glance

```
You → Perplexity (plan) → /spec/sessions/[date]-[topic]/handoff.md
→ git push → git pull in editor
→ Codex (execute) → /spec/sessions/[date]-[topic]/executor.md + updates /spec/status/
→ git push → git pull
→ You → Perplexity reads updates → next session
```

---

## How to Start a Planning Session (Perplexity)

### Step 1 — Create the session folder

Before opening Perplexity, create the session folder manually or via terminal:

```
spec/sessions/YYYY-MM-DD-NNN-topic/
```

Example: `spec/sessions/2025-01-20-003-cli-engine-architecture/`

NNN is a zero-padded session number (001, 002, 003...). Topic is a short slug.

### Step 2 — Open Perplexity with this exact prompt structure

```
[SESSION START]
Session: YYYY-MM-DD-NNN-topic

Please read the following spec files before responding:
- spec/core/context.md [paste content]
- spec/core/config-matrix.md [paste content]
- spec/core/stack.md [paste content]
- spec/core/constraints.md [paste content]
- spec/core/decisions.md [paste last 5 entries]
- spec/status/progress.md [paste content]
- spec/status/next-session.md [paste content]
- spec/sessions/[last session]/executor.md [paste content if exists]

Today's focus: [describe what you want to plan or decide]

[Your question or task]
```

> Tip: You don't need to paste every file every time. Always paste `context.md`, `config-matrix.md`, `progress.md`, and `next-session.md`. Paste `decisions.md` (last 5) and the last `executor.md` for continuity. Paste `constraints.md` when working on anything with edge case risk.

### Step 3 — Work through the session

Ask questions, get architecture decisions, research results, implementation plans. Let Perplexity do the research and planning. Push back if something violates architecture rules.

### Step 4 — Close the planning session

Before ending, ask Perplexity to write:

```
Please write:
1. planner.md for this session — your notes, decisions, and research summary
2. handoff.md — the specific instructions for the coding agent
Use the output format from AGENTS.md.
```

Paste the output into the correct session folder files. Commit and push.

---

## How to Start an Execution Session (Codex / Coding Agent)

### Step 1 — Pull latest

```bash
git pull
```

### Step 2 — Open your coding agent

Point it to the repo. If using Codex, it will read AGENTS.md automatically. If using another agent, paste AGENTS.md content at the start of the conversation.

### Step 3 — Start the session with this prompt

```
[SESSION START]
Please read AGENTS.md fully, then read the spec files in the order listed there.

Current session folder: spec/sessions/YYYY-MM-DD-NNN-topic/
Your instructions are in: spec/sessions/YYYY-MM-DD-NNN-topic/handoff.md

Begin only after you have confirmed you have read all required spec files.
```

Let the agent confirm it has read everything before it starts coding.

### Step 4 — Review work as it progresses

Check that the agent is not:

- Adding framework logic to the core engine
- Duplicating shared utilities
- Hardcoding config combinations
- Ignoring edge cases

If it drifts, redirect with: "This violates the architecture rule in AGENTS.md — [specific rule]. Please revise."

### Step 5 — Close the execution session

Before ending, ask the agent to:

```
Please write executor.md for this session using the format in AGENTS.md.
Then update spec/status/progress.md and spec/status/next-session.md.
If you made any architectural decisions or found new edge cases,
append them to spec/core/decisions.md and spec/core/constraints.md respectively.
```

Review the output, paste it into the correct files, commit and push.

---

## How to Close a Session Cleanly

**For planning sessions (Perplexity):**

1. Get `planner.md` and `handoff.md` written
2. Paste into session folder
3. Update `spec/status/next-session.md` if next topic is clear
4. Commit: `git commit -m "spec: session YYYY-MM-DD-NNN-topic planning complete"`
5. Push

**For execution sessions (Codex):**

1. Get `executor.md` written
2. Confirm `progress.md` is updated
3. Confirm `decisions.md` and `constraints.md` are appended if needed
4. Commit all code + spec changes: `git commit -m "feat: [what was built] + spec: session notes"`
5. Push

---

## How to Hand Off to a New Chat

When a chat context gets too long or you need to start fresh:

### For Perplexity (new chat):

Use the same Step 2 prompt from "How to Start a Planning Session" above. Always paste the current spec files. The spec files ARE the memory — the chat history is not needed.

### For Codex / coding agent (new chat):

```
[CONTEXT HANDOFF]
I am continuing work on the [YOUR PROJECT NAME].
Please read AGENTS.md fully. It contains all architecture rules and
instructions for reading the /spec folder.

The last execution session was: spec/sessions/YYYY-MM-DD-NNN-topic/
Read executor.md in that folder to understand what was last completed.
Then read spec/status/progress.md for current build state.
Your next task is in spec/status/next-session.md.
```

The agent should never need your chat history. If it does, the spec files are incomplete — update them.

---

## Setting Up Your Planning Agent (First Time Only)

1. Open `docs/PLANNER-INSTRUCTIONS.md`
2. Copy the **Generic Template** section
3. Fill in every placeholder for your project
4. Paste into your planning agent's custom instructions UI (Perplexity: Settings → Custom Instructions, or equivalent)
5. The [YOUR PROJECT NAME] example in the same file shows exactly what a filled-in version looks like

You only do this once per project. After that, the custom instructions are always active and you just paste spec file contents at the start of each session.

---

## Quick Reference Card

| Situation                        | What to do                                                            |
| -------------------------------- | --------------------------------------------------------------------- |
| Starting a new planning session  | Paste core spec files + next-session.md into Perplexity               |
| Starting a new execution session | Point agent to AGENTS.md + current session handoff.md                 |
| Agent is going off-track         | Quote the specific AGENTS.md rule being violated                      |
| Chat is getting too long         | Start new chat, use handoff prompt above                              |
| Session is done                  | Get planner.md/executor.md written, update status files, commit, push |
| New edge case found              | Append to constraints.md immediately, don't defer                     |
| New decision made                | Append to decisions.md immediately, don't defer                       |
| Stack choice confirmed           | Update stack.md                                                       |
| Something is TBD                 | Leave it TBD in stack.md, add a question to next-session.md           |

---

## Naming Conventions

**Session folders:** `YYYY-MM-DD-NNN-short-topic-slug`

- `2025-01-20-001-project-setup`
- `2025-01-21-002-cli-framework-research`
- `2025-01-22-003-config-resolver-architecture`

**Commit messages:**

- Planning: `spec: session NNN [topic] - planning`
- Execution: `feat: [what] + spec: session NNN notes`
- Spec-only updates: `spec: update [file] - [reason]`

**Branch names:**

- `feature/NNN-short-topic` matching session number where applicable

