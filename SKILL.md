---
name: external-memory
description: Set up or maintain the "external memory" pattern for AI coding agents in a repo — four markdown files (AGENTS.md for norms/commands/gotchas, PLAN.md for checklist + definition of done, PROGRESS.md for a short running log, VERIFY.md for exact commands that prove things work) plus one-line pointer files (CLAUDE.md, GEMINI.md) so every major agent tool reads the same canonical AGENTS.md. Use this skill when the user says things like "set up agent memory", "scaffold agent docs", "keep context fresh", "external memory pattern", "AGENTS.md", "PROGRESS.md", "the four-file pattern", "migrate CLAUDE.md", or "backfill PROGRESS from git history"; when starting work on a fresh repo and wanting durable context for future sessions; when migrating an existing CLAUDE.md-only repo onto the cross-tool AGENTS.md standard; or when an existing repo already has these files and they need updating, back-filling, or a staleness check. Handles five modes: init (scaffold from templates), migrate (promote substantive CLAUDE.md content into AGENTS.md and replace with pointer), update (apply the ritual after recent work via git log), harvest (bootstrap PROGRESS.md from N days of git history), and check (deep staleness audit including tech-stack drift, broken file references, and dangling "Next:" items).
---

# external-memory

The pattern: a small set of markdown files at the repo root that serve as
durable context for AI coding agents across sessions, so a cold start on
a future session can pick up exactly where the last one left off.

## The files

| File | Role | Tracked? |
|---|---|---|
| **AGENTS.md** | Canonical. Repo norms, tech stack, gotchas, conventions. | ✅ commit |
| **PLAN.md** | Checklist + definition of done. | ✅ commit |
| **PROGRESS.md** | Short running log: changes, failures, next steps. | ✅ commit |
| **VERIFY.md** | Exact commands that prove things work. | ✅ commit |
| **CLAUDE.md** | One-line pointer → AGENTS.md (Claude Code reads this by default). | ✅ commit |
| **GEMINI.md** | One-line pointer → AGENTS.md (Gemini CLI reads this by default). | ✅ commit |

AGENTS.md is the cross-tool convention promoted by [agents.md](https://agents.md);
the pointer files exist so each tool finds the same content under its
expected filename.

## Mode dispatch

If the user explicitly named a mode (`/external-memory check`, "migrate
my CLAUDE.md", "harvest the last 60 days"), use it. Otherwise auto-detect:

```bash
cd <repo-root>
canonical_count=$(ls AGENTS.md PLAN.md PROGRESS.md VERIFY.md 2>/dev/null | wc -l)
substantive_claude=$([[ -f CLAUDE.md ]] && [[ $(wc -l < CLAUDE.md) -gt 10 ]] && echo yes)
```

| State | Default mode |
|---|---|
| 0 canonical files, no substantive CLAUDE.md / GEMINI.md / AGENT.md | `init` |
| 0 canonical files, substantive CLAUDE.md (or AGENT.md or .cursorrules) exists | `migrate` |
| All 4 canonical files exist, AGENTS.md looks like ours (has a Companion files section) | `update` |
| All 4 canonical files exist, AGENTS.md doesn't look like ours | **ask the user** — don't assume it's safe to mutate |
| 1–3 canonical files exist (partial) | **ask the user** — may be a different convention; clarify before writing |

`harvest` and `check` are orthogonal — invoke whenever, on any state.

When in doubt, ask before writing.

---

## Mode: init

Goal: scaffold the six files, customized to the project — not generic placeholders.

### Steps

1. **Gather context.** Read in order, whichever exist:
   - `README.md` — for project purpose, stack.
   - `package.json`, `pyproject.toml`, `requirements.txt`, `go.mod`, `Cargo.toml`, `Gemfile` — for tech stack and `scripts:` / test commands.
   - `Makefile`, `justfile`, `tox.ini`, `noxfile.py` — for verify commands.
   - `CONTRIBUTING.md` — for conventions.
   - `git log --oneline -20` — for recent activity to seed PROGRESS.md.

2. **Read the templates** from `templates/` relative to this SKILL.md:
   - `templates/AGENTS.md`, `templates/PLAN.md`, `templates/PROGRESS.md`, `templates/VERIFY.md`

3. **Customize each template** and write to the repo root:
   - **AGENTS.md** — title with project name; "What this project is" distilled from README; "Tech stack" table populated from package files; "Non-negotiable principles" only if inferable (don't invent); "Conventions" with default ritual + anything found in CONTRIBUTING.md.
   - **PLAN.md** — leave "Pending" near-empty (user fills it); seed "Definition of done" from README's stated capabilities.
   - **PROGRESS.md** — one initial entry dated `$(date -u +%Y-%m-%d)`: "External memory files initialized" + one-line "Next:" inferred from PLAN.md if possible.
   - **VERIFY.md** — Pre-commit section populated with real commands you found (npm test, pytest, cargo test, make lint, etc.); Post-deploy left as `_TODO_` unless context tells you what to put.

4. **Write the pointer files** (3 lines each):
   ```markdown
   # CLAUDE.md

   This repo uses the cross-tool [AGENTS.md](AGENTS.md) convention.
   Claude Code reads this filename by default — the canonical content lives in AGENTS.md.
   ```
   Same shape for `GEMINI.md` (substitute "Gemini CLI reads this filename by default").

5. **Update .gitignore** if it excludes `*.md` with `!` allow-lists — add exceptions for the 6 files.

6. **Report.** Show a tree of what was created. Do **not** commit.

After init, if the repo has any git history, OFFER to run `harvest` next
to back-fill PROGRESS.md from the last N days of commits.

---

## Mode: migrate

Goal: lift content from a substantive `CLAUDE.md` (or `AGENT.md`, or
`.cursorrules`) into AGENTS.md, replace the original with a pointer, and
scaffold any missing canonical files. Equivalent to: "adopt the cross-tool
standard on a repo that already has agent docs in a tool-specific file".

### Steps

1. **Detect the source file.** First match wins:
   - `CLAUDE.md` (>10 lines and not already a pointer)
   - `AGENT.md` (singular, used by some tools)
   - `.cursorrules`
   - `GEMINI.md` (>10 lines and not already a pointer)

   If multiple substantive files exist, ask which one is the source of truth
   (or whether to merge content from all of them).

2. **Migrate content into AGENTS.md.**
   - Copy the source file's content as a starting point.
   - Replace the H1 title with `# AGENTS.md — <project name>`.
   - Add a "Companion files" section near the top:
     ```markdown
     Companion files in the same "external memory" set:
     - [PLAN.md](PLAN.md) — pending work + definition of done.
     - [PROGRESS.md](PROGRESS.md) — short running log.
     - [VERIFY.md](VERIFY.md) — exact commands to prove things work.
     ```
   - Add a "Conventions" subsection (or append to existing one) with the
     **external memory ritual** bullet — required so future agents know
     to update PROGRESS/PLAN/VERIFY.
   - Preserve everything else as-is. Don't restructure unless asked.

3. **Replace the source file with a pointer** (3 lines):
   ```markdown
   # CLAUDE.md

   This repo uses the cross-tool [AGENTS.md](AGENTS.md) convention.
   Claude Code reads this filename by default — the canonical content lives in AGENTS.md.
   ```
   Adapt the tool name to whichever file was the source.

4. **Scaffold any missing canonical files** (`PLAN.md`, `PROGRESS.md`,
   `VERIFY.md`) from templates, same as `init` but skipping AGENTS.md
   (already written in step 2).

5. **Write the other pointer files** (`CLAUDE.md` / `GEMINI.md` /
   whatever wasn't already the source) if they don't exist yet.

6. **Update .gitignore exceptions** as in `init` step 5.

7. **Report**, then offer `harvest` if there's git history worth mining.

---

## Mode: update

Goal: apply the ritual based on recent activity — log to PROGRESS.md,
tick PLAN.md, append to VERIFY.md if new checks emerged.

### Steps

1. **Find the cut-off date** — the date of the most recent PROGRESS.md
   entry (look for `## YYYY-MM-DD`). If PROGRESS.md is empty/missing, use
   7 days ago.

2. **Survey recent activity:**
   ```bash
   git log --since="$cutoff" --oneline
   git diff HEAD~10..HEAD --stat   # roughly
   git status                       # unstaged work counts too
   ```

3. **Draft a PROGRESS.md entry** at the top:
   ```
   ## YYYY-MM-DD — <one-line title>

   - <change 1 — file/area, one line>
   - <change 2>
   - Next: <single most important next step>
   ```
   Keep it tight. One line per change. The commit message has the details.

4. **Tick PLAN.md items.** For each `- [ ]` that the recent work
   completed, change to `- [x]`. If an item is fully done and stale,
   consider moving its essence into PROGRESS.md as a historical note and
   removing it from PLAN.md.

5. **Add to VERIFY.md if needed.** New endpoint to curl, new test
   command, new smoke check — add it.

6. **Do NOT auto-commit.** Show the proposed diff; user commits.

---

## Mode: harvest

Goal: bootstrap PROGRESS.md from existing git history — for repos that
adopt the pattern mid-project. Useful right after `init` or `migrate` on
a mature repo, or any time PROGRESS.md is suspiciously empty.

### Steps

1. **Decide the window.** Default: last 90 days. User may override
   ("harvest the last year", "harvest since v1.0", "harvest since the last
   PROGRESS entry").

2. **Pull commit history:**
   ```bash
   git log --since="$window" --date=short \
       --pretty=format:'%ad|%h|%s' --reverse
   ```
   Reverse so commits read oldest-first inside each cluster, then you
   reverse the cluster order to put newest on top of PROGRESS.md.

3. **Cluster by week.** Group commits by ISO week (`%Y-%W` from `%ad`).
   Within each week, deduplicate near-identical subjects (e.g. multiple
   "fix typo" commits → one bullet).

4. **For each weekly cluster, write a PROGRESS entry:**
   - Title: pick the most descriptive commit subject as the theme, or
     synthesize one from the changed paths (e.g. "auth refactor" if most
     commits touch `app/auth.py`).
   - Bullets: one per significant commit (or commit cluster). Use commit
     hashes when helpful (`ed9a8d5`).
   - "Next:" line: extract from the LAST commit in the week if it says
     "WIP", "todo", or similar; otherwise omit.

5. **Insert harvested entries at the top of PROGRESS.md**, newest first,
   above any existing entries. Add a footer note marking where the
   harvest ended:
   ```
   <!-- ↑ harvested 2026-05-23 from git log since 2026-02-22 -->
   <!-- For commits older than the harvest window, see `git log`. -->
   ```

6. **Cap aggressiveness.** Default cap: 12 weeks of entries to avoid
   flooding the file. If history is longer than the window, that's fine —
   the footer note tells future readers where to look.

7. **Do NOT auto-commit.** Show the user the proposed PROGRESS.md, ask
   for any tone/aggregation adjustments, then they commit.

---

## Mode: check

Goal: deep staleness audit; don't auto-fix.

### Steps

1. **PROGRESS.md staleness** — flag if the most recent `## YYYY-MM-DD`
   entry is older than 30 days. Surface as: "Last PROGRESS entry: 47 days
   ago — consider running update mode."

2. **PLAN.md hygiene**:
   - Items marked `[x]` that should probably move to PROGRESS.md as
     historical notes.
   - Items mentioning paths/files in backticks that no longer exist
     (`grep -oE '\\\`[^\\\`]+\\\`'` then `test -e`).
   - Items pointing to PR/issue numbers — verify they're still relevant if
     gh CLI is available (`gh pr view <n>` / `gh issue view <n>`).

3. **VERIFY.md hygiene** — any command line that references a binary,
   script path, URL, or test file that doesn't exist:
   - Binaries: `command -v <name>` for the first word.
   - Paths: regex `[\./][\w/.-]+\.(sh|py|js|ts|rb|go)` then `test -e`.

4. **Tech-stack drift** — parse the "Tech stack" table in AGENTS.md (the
   right-hand column). Compare against the actual dependencies:
   - `package.json` `dependencies` + `devDependencies`
   - `requirements.txt` / `pyproject.toml` `[project.dependencies]`
   - `go.mod` requires
   - `Cargo.toml` `[dependencies]`
   Flag anything in AGENTS.md that isn't installed, or any major
   framework in the install list that AGENTS.md doesn't mention.

5. **Dangling "Next:" items** — for each `## YYYY-MM-DD` entry in
   PROGRESS.md, extract the `Next:` line. Check whether its content
   (case-insensitive substring match) appears as a `- [ ]` item in
   PLAN.md. If not, flag as "PROGRESS promised X, never made it to PLAN".

6. **Pointer-file drift** — confirm `CLAUDE.md` and `GEMINI.md` are still
   <=10 lines and point to AGENTS.md. If content has crept in (someone
   edited the pointer instead of AGENTS.md), flag it.

7. **Report findings as a numbered list.** Ask whether to apply each fix.
   Don't auto-fix.

---

## Constraints

- **Never commit on the user's behalf** unless they explicitly say
  "commit". The skill writes files; the user decides commits.
- **AGENTS.md is canonical.** CLAUDE.md and GEMINI.md MUST be pointers
  (3-line files). Never duplicate substantive content into them.
- **Don't duplicate across the four canonical files.** PLAN.md tracks
  status, PROGRESS.md tracks history, VERIFY.md tracks how-to-check,
  AGENTS.md tracks norms. If the same fact wants to appear in two files,
  link instead.
- **Keep PROGRESS.md entries short.** One line per non-trivial change.
  The commit message has the details.
- **Templates live in `templates/`** relative to this SKILL.md. Read them
  with the absolute path to this skill directory + `/templates/<file>` —
  do not assume a working-directory location.
- **Don't invent norms.** If you can't infer something for AGENTS.md
  from existing docs, leave the `_TODO_` marker for the user to fill
  rather than fabricating.
- **Migrate, don't merge silently.** In migrate mode, if both
  `CLAUDE.md` and `AGENT.md` exist with substantive content, ask the
  user which is the source of truth.
- **Cap harvest output.** Don't generate 50 PROGRESS entries from a
  2-year history. Default cap is 12 weekly entries; ask before exceeding.
