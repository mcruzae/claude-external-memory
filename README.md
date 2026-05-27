# external-memory

A Claude Code skill that scaffolds and maintains a small set of markdown
files at the root of a repo, so AI coding agents (Claude Code, Cursor,
Codex, Aider, Gemini CLI…) can pick up exactly where the last session
left off.

Based on the "external memory" pattern from Anthropic's [Tip 10 — Keep
Context Fresh](https://www.anthropic.com/) (the four-file pattern shown
in their Claude Code best-practices talk).

---

## Why?

Every new AI coding session starts cold. The model can read your code,
but it doesn't know:

- What you tried yesterday that didn't work.
- What's pending vs done.
- Which tests / lint / smoke checks actually catch regressions in this repo.
- The norms you've established ("don't touch sshd_config", "no force-push",
  "we use rclone, not duplicity").

Stuffing this into `CLAUDE.md` works — until you switch tools (Cursor,
Codex, Gemini) and have to duplicate it. Or until `CLAUDE.md` grows into
an unreadable monolith that mixes norms, status, and history.

The pattern: **split that context across four small, focused files**, with
a canonical filename that every modern agent tool reads.

---

## The pattern

| File | Role | Lifespan |
|---|---|---|
| **AGENTS.md** | Canonical. Repo norms, tech stack, gotchas, conventions. | Stable. Edit rarely. |
| **PLAN.md** | Work by horizon (Now / Next / Later / Blocked) + definition of done. | Edit as work lands. |
| **PROGRESS.md** | Per-session log (Done / In progress / Next / Notes for future me). Newest on top. | Append-only. |
| **VERIFY.md** | Exact commands that prove things work (lint, tests, smoke). | Edit when a new check earns its keep. |
| **CLAUDE.md** | 3-line pointer → AGENTS.md (Claude Code reads this filename). | Set once. |
| **GEMINI.md** | 3-line pointer → AGENTS.md (Gemini CLI reads this filename). | Set once. |

`AGENTS.md` is the [cross-tool standard](https://agents.md). The pointer
files exist so every tool finds the same content under its expected
filename.

---

## Installation

The skill lives at `~/.claude/skills/external-memory/` on each machine.

### Clone directly into the skills directory

```bash
git clone https://github.com/mcruzae/claude-external-memory.git \
          ~/.claude/skills/external-memory
```

Restart Claude Code so it picks up the new skill (skills load at session start).

### Updating to a newer version

```bash
cd ~/.claude/skills/external-memory && git pull
```

### Multiple machines

Clone on each machine. Or put the skills directory in Dropbox / iCloud
and symlink — your call. `git pull` works fine for the one-source-of-truth
flow.

---

## Usage

Invoke with `/external-memory` in any repo. The skill detects state and
dispatches automatically; you can also name a mode explicitly:

```
/external-memory              # auto-detect mode
/external-memory init         # explicit
/external-memory migrate
/external-memory update
/external-memory harvest
/external-memory check
```

### Typical workflows

**New project, fresh repo:**

```
mkdir my-project && cd my-project
git init
# (optional but helpful: write a quick README.md first)
/external-memory             # → init mode; scaffolds the 6 files
# fill in the _TODO_ markers, commit
```

**Existing repo with CLAUDE.md (migrating to the cross-tool standard):**

```
cd existing-repo
/external-memory             # → migrate mode (detects substantive CLAUDE.md)
# review the new AGENTS.md, commit
```

**Mature repo, want to back-fill the running log:**

```
/external-memory harvest     # default: last 90 days, capped at 12 weeks
# review the generated PROGRESS.md entries, edit tone, commit
```

**After landing a chunk of work:**

```
/external-memory             # → update mode (drafts a PROGRESS entry, ticks PLAN items)
# review diff, commit
```

**Every few weeks (or before a hand-off to someone else):**

```
/external-memory check       # audit for stale entries, broken refs, tech-stack drift
# decide which findings to act on
```

---

## Modes in detail

### `init`

Scaffolds the six files in a repo that has none of them. Reads
`README.md`, package-manager files (`package.json`, `pyproject.toml`,
`requirements.txt`, `go.mod`, `Cargo.toml`, `Gemfile`), `Makefile`,
`tox.ini`, and `git log` to customize the templates. Doesn't fabricate
content — leaves `_TODO_` markers where it can't infer something.

### `migrate`

Detects an existing `CLAUDE.md` (or `AGENT.md`, or `.cursorrules`) with
substantive content. Lifts the content into `AGENTS.md`, replaces the
original file with a 3-line pointer, and scaffolds any missing companion
files. Preserves your existing structure — doesn't restructure unless
asked.

### `update`

Reads `git log` since the most recent `PROGRESS.md` entry, drafts a new
entry, ticks completed `PLAN.md` items, appends to `VERIFY.md` if new
checks emerged. Shows the diff; doesn't commit.

### `harvest`

Bootstraps `PROGRESS.md` from existing git history. Useful when adopting
the pattern mid-project. Clusters commits by week, picks themes, caps at
~12 entries by default so you don't get flooded.

### `check`

Deep audit:

- `PROGRESS.md` stale (>30 days since last entry)
- `PLAN.md` items referencing files that no longer exist
- `VERIFY.md` commands referencing missing binaries / scripts
- AGENTS.md tech-stack table drift vs actual `package.json` etc.
- `PROGRESS.md` "Next:" items that never became `PLAN.md` entries
- Pointer files that have grown beyond 10 lines (someone edited the wrong file)

Reports findings as a numbered list; doesn't auto-fix.

---

## Conventions the skill enforces

- **AGENTS.md is canonical.** CLAUDE.md and GEMINI.md must stay as 3-line
  pointers. Never duplicate substantive content into them.
- **PROGRESS.md is append-only and short.** One entry per work session
  (Done / In progress / Next / Notes for future me), one line per bullet.
  Commit messages have the details.
- **No auto-commit.** The skill writes files; you commit.
- **Don't fabricate norms.** If something can't be inferred, leave a
  `_TODO_` marker for the human.

---

## Customizing templates

Templates live in [`templates/`](templates/). Edit them and your next
`init` / `migrate` will use the new versions. Existing repos using the
old templates aren't auto-upgraded — that's a future enhancement (see
[issues](https://github.com/mcruzae/claude-external-memory/issues)).

---

## Trigger phrases

The skill's description field includes a wide net of trigger phrases so
Claude routes to it without you having to type `/external-memory`:

- "set up agent memory"
- "scaffold agent docs"
- "keep context fresh"
- "external memory pattern"
- "AGENTS.md" / "PROGRESS.md" / "the four-file pattern"
- "migrate CLAUDE.md"
- "backfill PROGRESS from git history"

---

## License

[MIT](LICENSE) — do whatever you want with it. Attribution appreciated but not required.
