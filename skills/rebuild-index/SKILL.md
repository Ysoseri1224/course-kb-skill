---
name: rebuild-index
version: 1.0.1
description: |
  Rebuild index.md from current vault state. Maintenance tool — use when:
  - "重建索引" / "索引不对了" / "index 过期了"
  - "rebuild index" / "index is stale"
  - /lint reports multiple [STALE INDEX] issues
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
---

# /rebuild-index

Rebuild `knowledge/index.md` from the current vault state.

## Trigger

User asks to rebuild, regenerate, or fix the index. Also useful when /lint reports multiple `[STALE INDEX]` issues.

## Procedure

1. Read `.kb-config.md` for vault paths.
2. Read existing `index.md` (preserve manually written notes unless schema says index is fully generated).
3. Glob all `knowledge/chapters/*.md` — read frontmatter for title and sequence.
4. Glob all `knowledge/concepts/*.md` — read frontmatter for title.
5. Sort chapters by sequence number.
6. Sort concepts alphabetically.
7. Preserve existing `## Coverage Gaps` entries unless clearly resolved (concept/chapter now exists with content).
8. Regenerate:
   - Chapter map (sequence, title, link)
   - Concept index (title, link, linked chapters)
   - Metadata (file counts, last rebuild date)
9. Verify all listed links resolve to existing files.
10. Write updated `index.md`.

## Output Format

The rebuilt `index.md` structure:

```markdown
# Course Knowledge Base Index

Updated: <YYYY-MM-DD>

## Chapter Map

| # | Chapter | Sources |
|---|---------|---------|
| 1 | [[chapters/ch01-xxx]] | resource-a |
| 2 | [[chapters/ch02-xxx]] | resource-b |

## Concept Index

| Concept | Chapters |
|---------|----------|
| [[concepts/concept-a]] | ch01, ch03 |
| [[concepts/concept-b]] | ch02 |

## Coverage Gaps

- <gap description> (since <date>)

## Metadata

- Chapters: N
- Concepts: N
- Resources: N
- Last rebuild: <YYYY-MM-DD>
```

## Rules

- Do NOT erase manually written notes unless schema says index is fully generated
- Do NOT remove coverage gaps that are still unresolved
- Remove gaps only when the corresponding concept/chapter now has substantive content
- All listed wikilinks must resolve
