# Contributing

This repository is a **template**: it is what every new vault starts from. Keep it small and generic.

## What belongs here

- The folder structure, `.rules/`, `index.md` and `log.md` formats.
- One short sample ingestion, so the conventions are visible at a glance.

## What does not

- Agents and skills. They live in the [`cortex` plugin](https://github.com/singullar/cortex-sdd), so there is one copy to maintain.
- Real knowledge, company names, personal paths or anything private.
- Third-party text without a license that allows republishing it.

## Changing the format

`.rules/ingestion.md` is a contract with the plugin's `ingestor` agent and vault linter. If you change templates, required frontmatter or the `index.md` / `log.md` formats, open a matching change in the plugin.

## Checks before opening a PR

- The sample still passes the linter: `python3 <plugin>/scripts/cortex_vault_lint.py <vault>`.
- Commit messages follow `.rules/git.md`.
