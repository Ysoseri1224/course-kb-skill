---
name: ingest-batch
version: 1.0.1
description: |
  Ingest all supported files in a directory. Internal skill called by /build.
  Use standalone when: user has a new batch of files to add to an existing vault.
  - "这个文件夹里的都加进去" / "batch import these files"
  - "新学期的材料都在这里" / "把这些新讲义都加进知识库"
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
  - PowerShell
---

# /ingest-batch <dir>

Ingest all supported files in a directory into the knowledge base.

## Trigger

User provides a directory path containing course source files.

## Supported file types

PDF, PPTX, DOCX, MD (as configured in `.kb-config.md`).

## Behavior

1. Read `.kb-config.md` for configured tools and paths.
2. Glob the directory for supported file types.
3. Sort files in stable order (alphabetical, preserving numbering).
4. For each file, run the full `/ingest <file>` pipeline.
5. After all files processed, run `/lint`.
6. Report batch summary.

## Rules

- Do NOT skip files silently — report each file's status.
- If a file fails extraction, record the error and continue with next file.
- Do NOT overwrite existing canonical resources without explicit permission.
- If multiple files map to the same chapter, merge content incrementally.

## Summary Output

```
Batch complete.
─────────────────────────────
Files processed:       N
Skipped (existing):    N
Failed:                N
New resources created: N
New chapters created:  N
New concepts created:  N
Concepts updated:      N
Broken links:          N
Coverage gaps:         N
```

If any files failed, list them with error reason.

## Pause Conditions

Same as /ingest — pause only on:
- Resource already exists (per file)
- Extraction materially disagrees
- Source corruption
- Concept conflict that changes meaning

Do NOT pause between files for routine progress.
