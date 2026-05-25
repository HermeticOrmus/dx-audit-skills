# Using this repo with Cursor

This project includes a Cursor project rule so the DX audit protocol is available when you work here.

## In this repository

1. Open the folder in Cursor.
2. The rule [`.cursor/rules/dx-audit.mdc`](.cursor/rules/dx-audit.mdc) is committed with `alwaysApply: false`, because a DX audit is situational rather than always-on. Invoke it by referencing the rule when you want to run an audit.
3. In Cursor, confirm under Settings → Rules, where `dx-audit` should appear.

## Use the same protocol in another project

Cursor (recommended): copy `.cursor/rules/dx-audit.mdc` into that project's `.cursor/rules/` directory (create the folders if needed). Merge with existing rules as you like.

Other AI coding tools: if a stack only supports a root instruction file, copy [`CLAUDE.md`](CLAUDE.md) into that project instead (or merge its contents into your existing instructions). Most modern AI coding tools (Claude Code, Continue, Cline, Windsurf, Aider) read a root-level instruction file.

## Optional: personal Agent Skills

If you want the same protocol as a reusable skill under `~/.cursor/skills`, use [`skills/dx-audit/SKILL.md`](skills/dx-audit/SKILL.md). Copy or symlink it into your personal skills directory.
