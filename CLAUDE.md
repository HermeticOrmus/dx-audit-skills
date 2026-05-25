# CLAUDE.md

A protocol for auditing and improving developer experience (DX) in a codebase. Merge with project-specific instructions as needed.

Developer experience is everything a contributor touches before they write their first useful line: the README, the setup steps, the scripts, the contributing guide, the env docs, the error messages, the tooling. When any of these is missing or wrong, every new contributor pays the cost silently, and the maintainer pays it again every time they answer the same question.

**Tradeoff**: this protocol biases toward measuring friction before fixing it. For a one-file script with no contributors, use judgment.

## What to inspect

Walk the codebase as a new contributor would, in this order. For each dimension, record what exists, what is missing, and whether what exists is accurate.

### 1. README

The first file a contributor opens.

- Does it say what the project is in the first paragraph?
- Does it have a quickstart that gets to a running state?
- Are the commands in it copy-pasteable and current?
- Does it link to deeper docs rather than trying to be all of them?

### 2. Quickstart and setup

The path from `git clone` to a running app.

- Is there a single documented sequence of commands?
- Does it list prerequisites (runtime version, package manager, services)?
- Are the steps in dependency order?
- Run them on a clean checkout. Count how many fail, stall, or require undocumented knowledge.

### 3. Scripts

The commands a contributor runs daily.

- Is there a task runner entry (`package.json` scripts, `Makefile`, `justfile`, `taskfile`)?
- Are the common verbs present: install, dev, build, test, lint, format?
- Do the script names match what the README and contributing guide reference?
- Is there a script for the thing every contributor does manually instead?

### 4. Contributing guide

The rules a contributor needs before opening a PR.

- Is there a `CONTRIBUTING.md` or equivalent section?
- Does it cover branch naming, commit format, PR expectations, and how to run tests?
- Does it match the actual workflow, or has the workflow drifted from the doc?

### 5. Environment docs

The configuration a contributor must supply.

- Is there an `.env.example` (or equivalent) listing every required variable?
- Is each variable annotated with what it is and where to get it?
- Does the app fail with a clear message when a variable is missing, or with a cryptic stack trace?

### 6. Error messages

The feedback a contributor gets when setup goes wrong.

- Do setup scripts fail loudly with an actionable message?
- Does a missing dependency, wrong runtime version, or absent env var produce a sentence a human can act on?
- Or does the contributor get a stack trace they have to decode?

### 7. Tooling

The guardrails that keep contributions consistent.

- Is there a linter, formatter, and type-checker configured?
- Are they wired to a pre-commit hook or CI so they run without being remembered?
- Is there an editor config (`.editorconfig`) so style is consistent across machines?

## How to score friction

For each dimension, assign a friction level. The goal is to find where the silent cost concentrates, not to produce a vanity number.

| Level | Meaning |
|---|---|
| None | Present, accurate, and discoverable. A new contributor succeeds without asking. |
| Low | Present but incomplete or slightly stale. Costs a minute of guessing. |
| Medium | Present but wrong, or absent where it matters. Costs a question to a maintainer. |
| High | Absent or actively misleading. Blocks a new contributor until someone intervenes. |

Two onboarding timings make the score concrete:

- Time to first run: from clean `git clone` to a running app or passing test command.
- Time to first contribution: from clean checkout to a correctly formatted, tested, ready-to-open PR.

A high-friction dimension is one that extends either timing past a reasonable threshold, or that cannot be completed at all from the documented steps.

## Report-only vs implement modes

The audit runs in one of two modes. State which mode is active before producing output.

### Report-only mode

Inspect, score, and report. Change nothing.

Produce a prioritized list of findings grouped by effort:

```text
## DX audit report

### Quick wins (5-15 min)
- [High] No quickstart in README; add a five-line clone-to-run sequence
- [Medium] `.env.example` lists 4 of 7 required variables

### Medium effort (30-60 min)
- [Medium] Test command undocumented; add to README and a `test` script
- [Low] CONTRIBUTING.md references a lint step that no script exists for

### Larger work (2+ hours)
- [Medium] No type-checker configured; wire tsc + pre-commit hook
```

Each finding names the dimension, the friction level, and the specific fix. No vague advice.

### Implement mode

Inspect, score, then apply fixes with approval, lowest-effort and highest-friction first.

- Make surgical changes. Add the missing README section, create the `.env.example`, add the script, fix the error message. Do not refactor adjacent code.
- For each fix, verify it: run the quickstart you just wrote on a clean checkout, run the script you just added, trigger the error path you just improved.
- Stop after each group and report what changed and what was verified.

Default to report-only when the mode is unstated. Implementing changes a contributor never reviewed is itself a DX failure.

## Verify before claiming a fix

Every documentation fix is a hypothesis about what works. Test it the way a new contributor would.

- Run the quickstart on a clean checkout, not on your already-configured machine.
- Run each documented script and confirm it does what the doc says.
- Trigger the missing-env-var and wrong-version paths and read the message a contributor would see.
- A README that has not been run on a clean checkout is a guess, not a fix.

---

## Trigger this audit when

- A new contributor asks the same setup question that a previous one asked
- The README quickstart has not been run on a clean checkout since the stack changed
- Setup takes longer than the time-to-first-run threshold the project accepts
- Onboarding a teammate surfaces an undocumented step
- A dependency, runtime version, or env variable changed but the docs did not

---

**License**: MIT. Use it, fork it, merge it into your own.
