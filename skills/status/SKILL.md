---
name: status
version: 1.0.1
description: |
  Show knowledge base coverage summary. Use when:
  - "知识库现在什么情况" / "覆盖了多少" / "建到哪了"
  - "show KB status" / "how much is covered"
  - User wants a quick overview without details
allowed-tools:
  - Read
  - Grep
  - Glob
---

# /status

Summarize current knowledge-base coverage and configuration.

## Trigger

User asks about vault status, coverage, progress, or configuration state.

## Procedure

1. Read `.kb-config.md` for configuration.
2. Count files in each vault directory.
3. Read `index.md` for coverage gaps.
4. Check concept pages for empty required sections.
5. Check chapter pages for completeness.
6. Count exam questions and answer availability.
7. Identify last ingest date from file modification times or frontmatter.

## Output Format

```
Knowledge Base Status
─────────────────────
Chapters:        N  (N complete, N with empty sections)
Concepts:        N  (N linked, N orphaned)
Resources:       N  files extracted
Exam questions:  N  (N with answers, N without)
Practice docs:   N

Coverage Gaps:   N items flagged in index.md
Last ingest:     <YYYY-MM-DD> <filename>
Config:          .kb-config.md
Extraction:      <configured tools>
Reference:       <engine posture>
```

## Additional Details (if vault is non-empty)

After the summary, optionally list:
- Chapters with empty sections
- Orphaned concepts (not linked by any chapter)
- Most recent coverage gaps

Keep output concise. User can run `/lint` for full detail.
