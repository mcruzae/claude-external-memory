# AGENTS.md — _TODO: project name_

Working notes for any AI coding agent (Claude, Cursor, Codex, Aider, Gemini…)
and future-me on this repo. Read this before touching anything.

Companion files in the same "external memory" set:

- [PLAN.md](PLAN.md) — pending work + definition of done.
- [PROGRESS.md](PROGRESS.md) — short running log of changes, failures, next steps.
- [VERIFY.md](VERIFY.md) — exact commands to prove things still work.

Tool-specific pointer files ([CLAUDE.md](CLAUDE.md), [GEMINI.md](GEMINI.md))
exist so every major agent tool finds the same content — they all redirect here.

---

## What this project is

_TODO: 1–2 paragraphs distilled from README.md. What is this codebase for?
What problem does it solve? Who is it for?_

---

## Non-negotiable principles

_TODO: Things that must NOT happen, with the reason why. Examples:_

- _TODO: e.g., "Never touch the production database without explicit approval."_
- _TODO: e.g., "Don't force-push to main."_
- _TODO: e.g., "No external network calls in unit tests."_

If you don't have specific principles yet, delete the section. Don't fabricate.

---

## Tech stack (locked in)

_TODO: Table of layer → choice, populated from package.json /
requirements.txt / go.mod / etc. Example shape:_

| Layer | Choice |
|---|---|
| Lang/framework | _TODO_ |
| Package manager | _TODO_ |
| Testing | _TODO_ |
| Lint / format | _TODO_ |
| Build / deploy | _TODO_ |

If you find yourself reaching for something else, stop and ask first.

---

## Layout

_TODO: Brief tree of the most important directories. Skip if the README
already has it._

```
.
├── src/         _TODO_
├── tests/       _TODO_
└── …
```

---

## Conventions

- **Git:** small, focused commits. Branches for anything risky.
- **No emojis in code or commits** unless asked.
- **No comments explaining the obvious.** Only explain *why* when it's non-obvious.
- **External memory ritual** — two cadences, don't conflate them:
  - *During the session:* tick [PLAN.md](PLAN.md) items as they land, and
    add a check to [VERIFY.md](VERIFY.md) the moment a new way to verify
    something earns its keep.
  - *At session close:* write **one** [PROGRESS.md](PROGRESS.md) entry for
    the session (`Done` / `In progress` / `Next` / `Notes for future me`).
    Don't append a line per change mid-flow — the `Next` and `Notes` fields
    only make sense once the session ends. Running the external-memory skill
    in `update` mode drafts this entry from the git log.

_TODO: Add any project-specific conventions (commit message style, branch
naming, code-review norms)._

---

## Open questions / future work

_TODO: Things worth capturing so they don't get lost, but aren't pending
right now. Delete the section if empty._
