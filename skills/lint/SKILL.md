---
name: lint
version: 1.0.1
description: |
  Check vault health. Use when:
  - "检查一下知识库" / "有没有问题" / "看看有什么坏链接"
  - "check vault health" / "any broken links?"
  - User suspects something is wrong with the KB
  - After manual edits to vault files
  Maintenance tool — reports issues without auto-fixing.
allowed-tools:
  - Read
  - Grep
  - Glob
---

# /lint

Check vault health and report issues without auto-fixing.

## Trigger

User asks to check vault health, verify consistency, or find issues.
Also runs automatically at the end of `/ingest-batch`.

## Checks

Scan the vault and report these issue types:

| Code | Meaning |
|------|---------|
| `[BROKEN LINK]` | A wikilink points to a missing file |
| `[ORPHAN CONCEPT]` | A concept is not referenced by any chapter |
| `[ORPHAN CHAPTER]` | A chapter is not listed in index.md |
| `[INLINE DEF]` | A chapter appears to define a concept inline |
| `[EMPTY SECTION]` | A required section in a page is empty |
| `[MISSING SOURCE]` | A source link points to a missing resource |
| `[CONFLICT]` | Sources disagree on a concept or claim (## Conflicts exists) |
| `[EXAM NO ANSWER]` | Question has no answer key (informational only) |
| `[STALE INDEX]` | File exists but index.md does not list it |
| `[PROTECTED WRITE]` | Protected directory or append-only file was modified |

## Procedure

1. Read `.kb-config.md` for vault paths.
2. Glob all `.md` files in chapters/, concepts/, resources/, test/, practice/.
3. For each file, extract all `[[wikilinks]]`.
4. Check each wikilink resolves to an existing file.
5. Check every concept file is referenced by at least one chapter.
6. Check every chapter file is listed in index.md.
7. Check chapter files for patterns suggesting inline definitions.
8. Check required sections exist and are non-empty.
9. Check frontmatter `sources:` links resolve.
10. Check for `## Conflicts` sections (informational).
11. Check test questions for missing answers (informational).
12. Check index.md lists all existing chapter and concept files.

## Output Format

```
Vault Lint Report
─────────────────
[BROKEN LINK] chapters/ch01-example.md → [[concepts/missing-concept]]
[ORPHAN CONCEPT] concepts/example.md — not referenced by any chapter
[INLINE DEF] chapters/ch02-example.md:15 — possible inline definition
[EMPTY SECTION] concepts/xyz.md — ## Definition is empty
[STALE INDEX] concepts/new-concept.md — not in index.md

Summary: N issues (N critical, N informational)
```

## Severity

- **Critical** (should fix): BROKEN LINK, ORPHAN CONCEPT, ORPHAN CHAPTER, MISSING SOURCE, STALE INDEX
- **Warning** (review): INLINE DEF, EMPTY SECTION, PROTECTED WRITE
- **Info** (awareness): CONFLICT, EXAM NO ANSWER

## Rule

Do NOT repair automatically. Report only. User must explicitly ask for fixes.

## Inline Definition Detection

Flag a chapter line as possible inline definition if it matches patterns:
- `X is defined as...`
- `X refers to...`
- `X means...`
- A bold term followed by a colon and multi-sentence explanation
- Definition-like content NOT inside a wikilinked concept reference
