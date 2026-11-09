# Usage Rule

Operational defaults for any session working against this vault. Imperative voice: these are not suggestions.

---

## Ingest

Each ingestion is atomic and produces its own raw, source, concept, entity, index and log entries. Never combine several sources into a single ingestion.

With the `cortex` plugin, `/cortex:ingest` feeds a closed cycle's `SESSION-SUMMARY.md` through the pipeline in `.rules/ingestion.md`.

---

## Vault health

Run a vault integrity check:

1. After every ingest.
2. Before any reset, archive or bulk delete.
3. Periodically, as a sweep.

`.lint-report.md` is the single authoritative state of vault health. A failing check does not block ingestion. Frontmatter never carries health flags.

---

## Context budget

Read `index.md` first, then only the files it routes to. Never glob `wiki/**` or `raw/**` to load content speculatively. If the index does not surface a file, the file does not participate in the current task.

---

## Deprecation

When a new file supersedes an existing one:

- Add `supersedes: <old-slug>` to the new file's frontmatter.
- Add `superseded_by: <new-slug>` to the old file's frontmatter.

Do not delete superseded files: the link history must remain traversable.
