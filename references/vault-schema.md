# Vault Schema Reference

This document defines the vault directory structure, file responsibilities, and frontmatter schemas used by all course-kb skills.

## Directory Structure

```
knowledge/
├── index.md          — Course overview, chapter map, concept index, coverage gaps
├── chapters/         — Argument structure by chapter or lecture unit
├── concepts/         — Single source of truth for concept definitions
├── resources/        — Canonical extracted source Markdown (evidence layer)
├── test/             — Exam and test question database
│   └── resources/    — Original exam papers (protected, never modified)
└── practice/         — Generated answers, study guides, practice documents
```

## Directory Responsibilities

| Path | Purpose | Write rules |
|------|---------|-------------|
| `index.md` | Course map and gap tracker | Updated on every ingest |
| `chapters/` | Argument structure only — no concept definitions | Create/update on ingest |
| `concepts/` | Single source of truth for definitions | Create/update on ingest |
| `resources/` | Canonical extraction artifacts | Read-only after creation |
| `test/` | Question database | Append via ingest-exam |
| `test/resources/` | Original exam files | Never modified by skill |
| `practice/` | Generated outputs | Append-only, never edit existing |

## Chapter Frontmatter Schema

```yaml
---
title: "<Chapter Title>"
sequence: <N>
sources: ["[[resources/<source-slug>]]"]
updated: <YYYY-MM-DD>
---
```

## Chapter Body Structure

```markdown
# <Chapter Title>

## Argument Structure

## Core Concepts
- [[concept-a]]
- [[concept-b]]

## Key Claims & Emphases

## Connections to Other Chapters
- Builds on: [[chapters/chNN-xxx]]
- Feeds into: [[chapters/chNN-xxx]]

## Open Questions
```

## Concept Frontmatter Schema

```yaml
---
title: "<Concept Name>"
aliases: []
chapters: ["[[chapters/chNN-xxx]]"]
sources: ["[[resources/<source-slug>]]"]
updated: <YYYY-MM-DD>
---
```

## Concept Body Structure

```markdown
# <Concept Name>

## Definition

## Core Arguments

## Relationship to Other Concepts

## Lecturer's Emphasis

## Examples & Cases

## Exam Notes
```

## Resource File Rules

- One canonical resource per source file
- Preserve source order, page/slide markers, headings, bullets, tables
- Preserve speaker notes when available
- Do NOT summarize, define concepts, or insert outside knowledge
- Filename: `resources/<source-slug>.md`

## Practice File Schema

```yaml
---
query: "<original question>"
date: <YYYY-MM-DD>
sources: ["[[chapters/chNN-xxx]]", "[[concepts/concept-a]]"]
---
```

```markdown
# <Descriptive Title>

## Answer

## Knowledge Base References
- <claim>: [[concepts/concept-a#Core Arguments]]
- <claim>: [[chapters/chNN-xxx#Key Claims & Emphases]]

## Gaps & Uncertainties

## Related Exam Questions
```

## Index File Structure

`index.md` must contain:
- Chapter map (all chapter files listed with sequence)
- Concept index (all concept files listed)
- Coverage gaps (unresolved gaps from source evidence)
- Last ingest metadata

## Slug Rules

- Lowercase
- Hyphen-separated
- Deterministic
- Preserve course numbering where available
- Never rename existing files without explicit user approval
