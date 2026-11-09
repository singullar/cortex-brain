# cortex-brain

A template for a **knowledge vault operated by AI agents**. Raw content goes in; a structured, cross-linked wiki comes out, and every coding session can read from it.

It is the memory of [cortex-sdd](https://github.com/singullar/cortex-sdd), a Claude Code plugin for spec-driven development: `/cortex:explore` reads the vault before work starts, and `/cortex:ingest` writes what the cycle learned back into it.

> **Create your vault as a private repository.** It will hold what you know about your work.

## Quickstart

The plugin sets this up for you:

```text
/plugin install cortex --marketplace singullar/cortex-sdd
/cortex:install
```

`/cortex:install` creates a private copy of this template in your GitHub account and records its path. Run `/cortex:doctor` at any time to check the setup.

To do it by hand, use **Use this template** on GitHub, or:

```bash
gh repo create my-brain --template singullar/cortex-brain --private --clone
```

## What comes out

| Layer | Location | Contains |
|-------|----------|----------|
| Sources | `wiki/sources/` | Summary, key points and links for each ingested source |
| Concepts | `wiki/concepts/` | Terms, formulas and patterns, reused across sources |
| Entities | `wiki/entities/` | People, companies and tools referenced across the vault |

Every ingestion updates `index.md` (the router) and `log.md` (the audit trail).

## Structure

```text
.
├── CLAUDE.md        # Guardrails for AI agents
├── index.md         # Master router: always read first
├── log.md           # Ingestion audit log
├── raw/clippings/   # Verbatim captures
├── wiki/
│   ├── sources/     # Processed source summaries
│   ├── concepts/    # Terms and methodologies
│   └── entities/    # People, companies, tools
└── .rules/          # Ingestion, language, git and usage rules
```

## Starting empty

The template ships one sample ingestion so the conventions are visible. To start from nothing:

1. Delete every file under `wiki/sources/`, `wiki/concepts/`, `wiki/entities/` and `raw/clippings/`, except `.gitkeep`.
2. In `index.md`, keep the header and the three section headings. In `log.md`, keep the header and the empty table.
3. Commit.

## License

MIT. See [LICENSE](LICENSE). Your vault's content is yours.
