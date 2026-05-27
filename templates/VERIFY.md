# VERIFY.md — _TODO: project name_

Exact, observable commands that prove things still work — plus the bare
facts an agent or human needs to run and inspect the project at all. Every
command pairs with its expected outcome.

If a new "did this break something?" check earns its keep more than once,
add it here so future-me doesn't reinvent it.

---

## Key pages / surfaces

_TODO: The routes, screens, or entry points worth eyeballing after a
change. For a web app, list URLs. For a CLI, list the headline commands.
A reviewer (or screenshot/audit tool) starts here._

- `/` — _TODO: what it is_
- _TODO: other important route/screen_

## Start command

_TODO: The exact command(s) to run the project locally, and what a healthy
start looks like._

```bash
_TODO: e.g.  npm run dev          # then open http://localhost:3000
```
Expect: _TODO — e.g. "serves on :3000, no console errors"._

---

## Pre-commit (must all pass before committing)

_TODO: Fast offline checks. Populate from package.json scripts, Makefile,
tox.ini, etc. Examples below — replace with the project's real commands._

### Lint / type-check

```bash
_TODO: e.g.  npm run lint        # or:  ruff check .
_TODO: e.g.  npm run typecheck   # or:  mypy .
```
Expect: silent / 0 errors.

### Unit tests

```bash
_TODO: e.g.  npm test            # or:  pytest -q
```
Expect: all green.

### Build

```bash
_TODO: e.g.  npm run build       # or:  cargo build --release
```
Expect: silent success.

---

## Pre-deploy (must all pass before shipping)

_TODO: Everything that gates a release. Skip if the project doesn't deploy._

- All **Pre-commit** checks above.
- _TODO: the project's ship gate, if it has one — e.g. a `/ship-check`
  slash command that bundles responsive screenshots, an accessibility /
  performance audit of the Key pages above, and the Pre-commit checks. Run
  it here. (Delete this line if there's no such gate.)_
- _TODO: any other release-only check — a full e2e run, a staging smoke test._
- _TODO: manual verification step, if any._

---

## Smoke tests (live / post-deploy)

_TODO: Live-environment checks. Skip if not applicable._

```bash
_TODO: e.g.  curl -sI https://example.com/health | head -3
```
Expect: `HTTP/2 200`.

_TODO: 1-2 sentence description of the golden user path + the exact
command/URL that exercises it._

---

## Convention

When you add a feature or fix a bug:

1. Pick the verify step from this file that would have caught the regression. Run it.
2. If none would have, add a new one here as part of the same commit.
