# VERIFY.md — _TODO: project name_

Exact commands to prove things still work. Two flavors:

- **Pre-commit / local smoke** — fast, offline. Run before pushing.
- **Post-deploy smoke** — on the live environment, after a deploy.

If a new "did this break something?" check earns its keep more than once,
add it here so future-me doesn't reinvent it.

---

## Pre-commit smoke

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

## Post-deploy smoke

_TODO: Live-environment checks. Skip if not applicable._

### Health endpoint

```bash
_TODO: e.g.  curl -sI https://example.com/health | head -3
```
Expect: `HTTP/2 200`.

### Critical user flow

_TODO: 1-2 sentence description of the golden path + the exact command/URL
that exercises it._

---

## Convention

When you add a feature or fix a bug:

1. Pick the verify step from this file that would have caught the regression. Run it.
2. If none would have, add a new one here as part of the same commit.
