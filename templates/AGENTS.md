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

Concrete rules beat principles. Fill each with the project's actual
choices (pull from the PRD, CONTRIBUTING.md, `.pre-commit-config.yaml`);
delete a sub-header that genuinely doesn't apply rather than leaving it vague.

**Writing style** _(applies to docs, comments, commit text, UI copy)_:

- _TODO: e.g. plain English; no em-dashes; dates as YYYY-MM-DD._

**Code style:**

- _TODO: per language → linter/formatter + line length (e.g. "TS: eslint + prettier, 100 cols")._
- _TODO: folder / naming rules._
- **No comments explaining the obvious.** Only explain *why* when it's non-obvious.

**Commit messages:**

- _TODO: format — e.g. `type(scope): description`._
- _TODO: an example — e.g. `feat(auth): add password reset flow`._
- Small, focused commits. Branches for anything risky. No emojis unless asked.

**Things to never do:**

- _TODO: specific prohibited actions, each with the reason why._

**External memory ritual** — two cadences, don't conflate them:

- *During the session:* tick [PLAN.md](PLAN.md) items as they land, and
  add a check to [VERIFY.md](VERIFY.md) the moment a new way to verify
  something earns its keep.
- *At session close:* write **one** [PROGRESS.md](PROGRESS.md) entry for
  the session (`Done` / `In progress` / `Next` / `Notes for future me`).
  Don't append a line per change mid-flow — the `Next` and `Notes` fields
  only make sense once the session ends. Running the external-memory skill
  in `update` mode drafts this entry from the git log.

---

## Open questions / future work

_TODO: Things worth capturing so they don't get lost, but aren't pending
right now. Delete the section if empty._
