# Ingestion Rule

## Purpose

Defines the standard for processing any raw content into the vault. All agents performing
ingestion operations must follow this rule exactly.

---

## Source Types

| Type | Trigger | Raw slug prefix |
|------|---------|-----------------|
| Article | Blog post, paper, newsletter | `article-` |
| Web page | Product page, docs, reference | `web-` |
| Video | YouTube, podcast transcript | `video-` |
| Book | Chapter or excerpt | `book-` |
| Session | A work-cycle summary (`SESSION-SUMMARY.md` from `/cortex:end`) | `session-` |

---

## Required Steps (in order)

1. Save raw content to `raw/clippings/<slug>.md`
2. Generate source summary at `wiki/sources/<slug>.md`
3. Extract concepts → create/update files in `wiki/concepts/`
4. Extract entities → create/update files in `wiki/entities/`
5. Update `index.md` with links to all new/updated files
6. Append a row to `log.md`

---

## File Templates

### `raw/clippings/<slug>.md`

```markdown
---
title: <Original title>
source: <URL or "manual">
type: <article | web | video | book | session>
date: <YYYY-MM-DD>
---

<Raw content pasted verbatim — no edits>
```

### `wiki/sources/<slug>.md`

```markdown
---
title: <Original title>
source: <URL or "manual">
type: <article | web | video | book | session>
date: <YYYY-MM-DD>
tags: []
---

## Summary

<3–5 sentence summary of the main argument or content>

## Key Points

- <point 1>
- <point 2>
- <point 3>

## Concepts

<!-- Links to wiki/concepts/ files extracted from this source -->
- [[<concept-slug>]]

## Entities

<!-- Links to wiki/entities/ files extracted from this source -->
- [[<entity-slug>]]

## Raw

[[raw/clippings/<slug>]]
```

### `wiki/concepts/<slug>.md`

```markdown
---
title: <Concept name>
aliases: []
tags: []
---

## Definition

<Clear, concise definition in 2–4 sentences>

## Details

<Formulas, diagrams described in text, or expanded explanation>

## Related Concepts

- [[<related-concept-slug>]]

## Sources

- [[wiki/sources/<slug>]]
```

### `wiki/entities/<slug>.md`

```markdown
---
title: <Entity name>
type: <person | company | tool | framework>
tags: []
---

## Description

<Who or what this entity is, in 2–3 sentences>

## Relevance

<Why this entity appears in the vault>

## Sources

- [[wiki/sources/<slug>]]
```

---

## Frontmatter Schema

| Location | Required fields |
|----------|-----------------|
| `wiki/sources/` | `title`, `source`, `type`, `date` |
| `wiki/concepts/` | `title` |
| `wiki/entities/` | `title`, `type` |
| `raw/clippings/` | `title`, `source`, `type`, `date` |

---

## Slug Rules

- Derive slug from the source title: lowercase, kebab-case, max 6 words
- Examples:
  - "Attention Is All You Need" → `attention-is-all-you-need`
  - "Designing Data-Intensive Applications" → `designing-data-intensive-applications`
- Concepts and entities use their own name as slug, not the source slug

---

## Pre-write checks

- **Duplicate detection**: before creating raw or source files, run `grep -q "<slug>" index.md`. If it hits, abort the ingestion and return the existing path to the caller.
- **URL fetch**: try `WebFetch` first. On failure, retry once with header `User-Agent: Mozilla/5.0`. On second failure, prompt the user to paste the raw content manually.
- **Frontmatter fail-fast**: assemble each file in memory and validate its YAML frontmatter against the schema above before writing to disk.
- **Vault health**: when the caller has a vault linter available (the `cortex` plugin ships one), run it before step 1 and after step 6, and report (a) issues this ingest introduced and (b) issues that already existed. Never persist a health flag in frontmatter; `.lint-report.md` is the only authority on vault health.

---

## Extraction Criteria

### When to create a `concept`
- A term, formula, model, or methodology is explained in the source
- It is likely to appear in multiple future sources
- Examples: "transformer", "retrieval-augmented generation", "chain-of-thought"

### When to create an `entity`
- A named person, company, tool, or framework is referenced
- It has relevance beyond a single source
- Examples: "PostgreSQL", "Kubernetes", "a well-known author in your field"

### When NOT to create a new file
- The concept or entity already exists — update the existing file instead
- The reference is incidental (one mention, no explanation)

---

## Index and Log Update Format

This is the only place that defines how `index.md` and `log.md` are updated. Do not redefine these formats anywhere else.

### `index.md`

Append entries under the matching H2 section. Insertion only — never reorder, never digest content.

```markdown
## Sources (`wiki/sources/`)
- [[wiki/sources/<slug>]] — <one-line description> (<YYYY-MM-DD>)

## Concepts (`wiki/concepts/`)
- [[wiki/concepts/<slug>]] — <one-line definition>

## Entities (`wiki/entities/`)
- [[wiki/entities/<slug>]] — <type>: <one-line description>
```

### `log.md`

Append exactly one row to the end of the table per ingestion.

```
| YYYY-MM-DD | <type> | <title> | [[wiki/sources/<slug>]] |
```
