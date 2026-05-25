# Examples

Two worked audits. Each shows the codebase as found, the findings with friction levels, and the fixes applied.

---

## 1. A repo with a thin README and no quickstart

### Scenario

A Node service. The README is one paragraph describing what the service does and nothing else. A new contributor clones it and has no idea what to run.

```text
# orders-service

Handles order creation and fulfillment for the storefront.
```

### Audit

Run in report-only mode first. Walk the dimensions.

- README: present, but it is the only documentation. No quickstart, no commands. Friction: High.
- Quickstart and setup: there is no documented sequence. Reading `package.json` reveals a `dev` and a `start` script, but the contributor would have to find them. Friction: High.
- Scripts: `package.json` has `dev`, `start`, and `test`. They exist and work. Friction: None.
- Environment docs: the service reads `DATABASE_URL` and `STRIPE_KEY` from `process.env`, but there is no `.env.example`. The app crashes on boot with `TypeError: Cannot read properties of undefined`. Friction: High.
- Error messages: the boot crash is a raw stack trace, not a message naming the missing variable. Friction: Medium.

Time to first run from the documented steps: not achievable. The contributor cannot start the service without reading the source for env vars.

### Findings

```text
## DX audit report: orders-service

### Quick wins (5-15 min)
- [High] README has no quickstart. Add clone-to-run sequence using the existing dev/start/test scripts.
- [High] No .env.example. Create one listing DATABASE_URL and STRIPE_KEY with annotations.

### Medium effort (30-60 min)
- [Medium] Boot crashes with a raw stack trace when an env var is missing. Add a startup check that names the missing variable.
```

### Fixes applied (implement mode)

Add a quickstart to the README using the scripts that already exist:

```markdown
## Quickstart

Prerequisites: Node 20+, a Postgres database, a Stripe test key.

1. `npm install`
2. `cp .env.example .env` and fill in the values
3. `npm run dev` starts the service on http://localhost:3000
4. `npm test` runs the test suite
```

Create `.env.example` with every required variable annotated:

```bash
# Postgres connection string. Local: postgres://localhost:5432/orders
DATABASE_URL=

# Stripe secret key. Test keys start with sk_test_. Get one at dashboard.stripe.com.
STRIPE_KEY=
```

Replace the silent crash with a startup check that names the gap:

```js
const required = ['DATABASE_URL', 'STRIPE_KEY'];
const missing = required.filter((key) => !process.env[key]);
if (missing.length > 0) {
  console.error(`Missing required environment variables: ${missing.join(', ')}. See .env.example.`);
  process.exit(1);
}
```

### Verification

Run the new quickstart on a clean checkout. With an empty `.env`, the service now exits with `Missing required environment variables: DATABASE_URL, STRIPE_KEY. See .env.example.` instead of a stack trace. With the variables filled in, `npm run dev` starts the service. Time to first run dropped from not-achievable to a documented four-step sequence.

---

## 2. A project where the test command is undocumented

### Scenario

A Python library with a solid README: it has an install section, a usage example, and an API overview. A contributor wants to add a feature and run the tests before opening a PR. The README never mentions how.

### Audit

- README: clear and current for usage, but silent on development workflow. Friction for a reader: None. Friction for a contributor: Medium.
- Quickstart and setup: the usage quickstart works. There is no contributor setup (dev dependencies, test command). Friction: Medium.
- Scripts: there is no task runner. Tests run with `pytest`, but only if the contributor knows to install the dev extras first (`pip install -e ".[dev]"`). Neither command is written down. Friction: High.
- Contributing guide: no `CONTRIBUTING.md`. The commit and PR conventions live only in the maintainer's head. Friction: Medium.
- Tooling: `pyproject.toml` configures `ruff` and `mypy`, and there is a `.pre-commit-config.yaml`, but the README never tells a contributor to run `pre-commit install`. The hooks exist but never fire for a new contributor. Friction: Medium.

Time to first contribution from the documented steps: not achievable. The contributor can install and use the library, but cannot discover how to test or lint a change.

### Findings

```text
## DX audit report: the-library

### Quick wins (5-15 min)
- [High] Test command undocumented. Add a "Development" section: install dev extras, run pytest.
- [Medium] pre-commit hooks configured but never installed by contributors. Document `pre-commit install`.

### Medium effort (30-60 min)
- [Medium] No CONTRIBUTING.md. Add branch naming, commit format, and the test/lint expectations for a PR.
```

### Fixes applied (implement mode)

Add a development section to the README:

```markdown
## Development

1. `pip install -e ".[dev]"` installs the library plus test and lint tools
2. `pre-commit install` wires the ruff and mypy hooks to run on commit
3. `pytest` runs the test suite
4. `ruff check . && mypy .` lints and type-checks manually if needed
```

Add a `CONTRIBUTING.md` that captures the workflow which previously lived only in the maintainer's head:

```markdown
# Contributing

- Branch from `main` as `feature/<description>` or `fix/<description>`.
- Commit messages: `type(scope): description` (feat, fix, docs, refactor, test, chore).
- Before opening a PR: `pytest` passes and `pre-commit run --all-files` is clean.
- PRs should be scoped to one change. Keep the diff reviewable.
```

### Verification

On a clean checkout, follow the new development section. `pip install -e ".[dev]"` pulls the test tools, `pre-commit install` wires the hooks, and `pytest` runs the suite. Make a trivial unformatted change and commit: the pre-commit hook now fires and reformats it, which it did not do for a new contributor before. Time to first contribution dropped from not-achievable to a documented four-step sequence plus a contributing guide.

---

## A note on what an audit is not

A DX audit is not a documentation rewrite. The fixes above are surgical: one README section, one `.env.example`, one startup check, one `CONTRIBUTING.md`. The point is to remove the specific friction a new contributor hits, not to produce exhaustive docs that go stale. Every fix traces to a scored finding, and every fix is verified on a clean checkout, because a README that has not been run on a clean checkout is a guess.

## Further reading

- [`setup-env-skills`](https://github.com/HermeticOrmus/setup-env-skills): when a setup finding is high-friction, this sets the environment up
- [`claude-md-overhaul-skills`](https://github.com/HermeticOrmus/claude-md-overhaul-skills): the AI-context counterpart, auditing the CLAUDE.md / MEMORY.md layer
