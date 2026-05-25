<p align="center">
  <img src="https://ormus.solutions/mascot/pixellab_liquid_to_terminal.gif" alt="DX Audit Skills" width="128" style="image-rendering: pixelated;" />
</p>

<h1 align="center">DX Audit Skills</h1>

<p align="center">
  <em>A Claude Code skill that audits developer experience — onboarding, docs, scripts, and tooling friction — and proposes fixes.</em>
</p>

<p align="center">
  <a href="https://github.com/HermeticOrmus/dx-audit-skills/stargazers"><img src="https://img.shields.io/github/stars/HermeticOrmus/dx-audit-skills?style=flat-square&color=aa8142" alt="Stars" /></a>
  <a href="https://github.com/HermeticOrmus/dx-audit-skills/blob/main/LICENSE"><img src="https://img.shields.io/github/license/HermeticOrmus/dx-audit-skills?style=flat-square&color=aa8142" alt="License" /></a>
  <a href="https://github.com/HermeticOrmus/dx-audit-skills/commits"><img src="https://img.shields.io/github/last-commit/HermeticOrmus/dx-audit-skills?style=flat-square&color=aa8142" alt="Last Commit" /></a>
  <img src="https://img.shields.io/badge/Claude_Code-aa8142?style=flat-square&logo=anthropic&logoColor=white" alt="Claude Code" />
</p>

---

## The problem

Developer experience is everything a contributor touches before they write their first useful line: the README, the setup steps, the scripts, the contributing guide, the env docs, the error messages, the tooling. High onboarding friction is silent. No one files a bug that says "the README took me an hour to decode." They just lose the hour, ask a maintainer the same question the last three contributors asked, and the cost never lands on a ledger.

This skill makes the cost visible. It walks the codebase the way a new contributor would, scores where friction concentrates, and either reports the findings or fixes them.

## Audit dimensions

| Dimension | What it inspects | Common high-friction failure |
|---|---|---|
| README | First-paragraph clarity, quickstart, current commands | No quickstart; reader cannot get to a running state |
| Quickstart and setup | The path from clone to running app, in dependency order | Steps fail on a clean checkout or assume undocumented knowledge |
| Scripts | install / dev / build / test / lint / format entries in a task runner | The command everyone runs is undocumented or has no script |
| Contributing guide | Branch naming, commit format, PR rules, how to run tests | The doc has drifted from the actual workflow |
| Environment docs | `.env.example` listing every variable, annotated | Missing variables; cryptic crash instead of a clear message |
| Error messages | Whether setup fails loudly and actionably | Stack trace where a sentence would do |
| Tooling | Linter, formatter, type-checker wired to hooks or CI | Guardrails exist but never run automatically |

Each dimension is scored None / Low / Medium / High friction. Two timings make the score concrete: time to first run (clone to running app) and time to first contribution (clone to a tested, formatted, ready PR).

## Report-only vs implement modes

The audit runs in one of two modes.

- Report-only: inspect, score, and produce a prioritized list of findings grouped by effort. Change nothing. This is the default.
- Implement: inspect, score, then apply fixes with approval, highest-friction and lowest-effort first. Each fix is verified on a clean checkout before it is claimed done.

Report-only is the default because applying changes a contributor never reviewed is itself a DX failure.

## Install

### As a project CLAUDE.md

Drop [`CLAUDE.md`](CLAUDE.md) at the root of your repository. Claude Code picks it up automatically. Merge with existing project instructions if any.

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/HermeticOrmus/dx-audit-skills/main/CLAUDE.md
```

### As a Claude Code skill

The same protocol is packaged as a skill under [`skills/dx-audit/`](skills/dx-audit/) for `~/.claude/skills/`. Copy or symlink the directory:

```bash
cp -r skills/dx-audit ~/.claude/skills/
```

### As a `/dx-audit` slash command

To invoke the audit as a slash command, drop the skill content into your commands directory:

```bash
curl -o ~/.claude/commands/dx-audit.md https://raw.githubusercontent.com/HermeticOrmus/dx-audit-skills/main/skills/dx-audit/SKILL.md
```

Then run `/dx-audit`, `/dx-audit --report-only`, or `/dx-audit --implement`.

### In Cursor

See [`CURSOR.md`](CURSOR.md) for the Cursor-rule equivalent at [`.cursor/rules/dx-audit.mdc`](.cursor/rules/dx-audit.mdc).

### In other AI coding tools

If your tool reads a single instruction file at the project root, copy `CLAUDE.md` to whatever name your tool expects (`AGENTS.md`, `INSTRUCTIONS.md`, etc.).

## See also

- [`setup-env-skills`](https://github.com/HermeticOrmus/setup-env-skills): sets up a development environment for a project or technology, the fix side of a high-friction setup finding
- [`claude-md-overhaul-skills`](https://github.com/HermeticOrmus/claude-md-overhaul-skills): audits and improves the CLAUDE.md / MEMORY.md layer, the AI-context counterpart to a human-facing DX audit

## Contributing

PRs welcome, especially additional worked audits in [`EXAMPLES.md`](EXAMPLES.md), adaptations of `CURSOR.md` for other AI coding tools, and refinements to the friction-scoring rubric.

## License

MIT. Use it, fork it, merge it into your own CLAUDE.md.
