---
name: ingest
version: 1.0.1
description: |
  Ingest one course source file into the existing knowledge base. Use when:
  - "帮我加入这个新讲义" / "新来了一个PDF" / "把这个也加进知识库"
  - "add this file to the KB" / "ingest this lecture"
  - User provides a single new file path after the vault already exists
  - User says "又有新材料了" or mentions receiving new course content
  
  This is the INCREMENTAL entry point — adds to an existing vault.
  Runs fully automatically: extract → compare → write resource/chapter/concept → update index.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
  - PowerShell
---

# /ingest <file>

Ingest one course source file into the structured knowledge base.

## Trigger

User provides a file path to ingest (PDF, PPTX, DOCX, or Markdown).

## Pipeline

```
source file → extract → compare → canonical resource → survey vault
→ analyze → write chapters → write concepts → update index → verify
```

This pipeline runs AUTOMATICALLY after setup. Pause only on conflicts or errors.

## Step 1 — Preflight

1. Read `.kb-config.md` for configured tools and paths.
2. Read local `CLAUDE.md` or equivalent rules if present.
3. Verify the source file exists at the given path.
4. Identify file type (PDF/PPTX/DOCX/MD).
5. Determine which extraction tools to use from config.
6. Check whether `resources/<source-slug>.md` already exists.
   - If exists: STOP and ask user — do not overwrite without permission.

## Step 2 — Multi-extraction

Run all configured extraction tools for the file type:

**PDF routing:**
- MinerU: `mineru <file> -o .extraction-cache/<slug>/mineru/`
- kb-extract: `kb extract <file> -o .extraction-cache/<slug>/kb-extract.md`

**PPTX routing:**
- kb-extract: `kb extract <file> -o .extraction-cache/<slug>/kb-extract.md`
- python-pptx fallback (preserves speaker notes):
  `.venv/Scripts/python -c "from pptx import Presentation; ..."`

**DOCX routing:**
- kb-extract: `kb extract <file> -o .extraction-cache/<slug>/kb-extract.md`
- markitdown fallback: `.venv/Scripts/python -m markitdown <file>`

Store candidates in `.extraction-cache/<slug>/` for comparison.

## Step 3 — Compare extraction candidates

Compare candidates for:
- Page/slide count mismatch
- Major missing text blocks
- Heading hierarchy differences
- Table corruption or loss
- Speaker note presence/absence
- OCR uncertainty or encoding artifacts

**Auto-resolve:** If differences are minor, merge best parts automatically.

**Pause and ask user ONLY when:**
- One extraction is clearly incomplete (>20% content missing)
- Tables with likely exam-relevant content are corrupted
- OCR ambiguity changes meaning
- Source file appears damaged
- Candidates disagree so much that canonical creation would be unsafe

## Step 4 — Create canonical resource

Write exactly ONE file: `knowledge/resources/<source-slug>.md`

Rules (see `references/vault-schema.md` for full spec):
- Preserve source order
- Preserve page/slide markers
- Preserve headings, bullets, tables
- Preserve speaker notes
- Do NOT summarize, define concepts, or insert outside knowledge

## Step 5 — Survey existing vault

Before writing chapters/concepts:
1. Read `knowledge/index.md`
2. Glob `knowledge/chapters/*.md` — read frontmatter and headings
3. Glob `knowledge/concepts/*.md` — read filenames
4. Read related concept pages if likely affected
5. Identify existing chapter sequence and coverage gaps

## Step 6 — Analyze source

Analyze the canonical resource for:
1. Which chapter(s) it belongs to
2. Whether new chapters are needed
3. Every new concept introduced
4. Existing concepts reinforced or contradicted
5. Lecturer's argumentative structure
6. Repeated/emphasized terms (exam signals)
7. Examples and case studies
8. Connections to other chapters
9. Open questions or unresolved tensions

## Step 7 — Write chapter pages

See `references/vault-schema.md` for chapter schema.
See `references/writing-rules.md` for chapter writing rules.

Rules:
- No inline concept definitions — wikilink all concepts
- Write logical argument structure, not slide-by-slide notes
- Add `## Conflicts` if new source contradicts existing claims
- If chapter already exists, UPDATE it (append new sources, preserve existing)
- Update frontmatter `sources:` and `updated:` fields

## Step 8 — Write concept pages

See `references/vault-schema.md` for concept schema.
See `references/writing-rules.md` for concept writing rules.

Rules:
- Concepts are the ONLY place for definitions
- If concept already exists: append/update carefully, do NOT overwrite
- If sources conflict: note conflict explicitly, do not resolve silently
- Update frontmatter `chapters:`, `sources:`, `updated:` fields

## Step 9 — Update index

Update `knowledge/index.md` with:
- New chapters added to chapter map (with sequence)
- New concepts added to concept index
- Coverage gaps updated (add new gaps discovered, preserve existing unresolved)
- Last ingest metadata updated

## Step 10 — Verify

Before reporting completion, verify ALL of:
- [ ] All new wikilinks resolve to existing files
- [ ] Every new concept appears in index concept list
- [ ] Every new chapter appears in index chapter map
- [ ] No chapter contains inline concept definitions
- [ ] Resource file exists at expected path
- [ ] Concept pages link to relevant chapters
- [ ] Chapters link to relevant concepts
- [ ] `test/resources/` was not modified
- [ ] No existing practice files were edited

Report: files created/updated, new concepts, new chapters, any warnings.

## Step 11 — Scoped lint

Run a lightweight lint on files touched in this ingest only:
- The new/updated resource file
- The new/updated chapter file(s)
- The new/updated concept file(s)
- index.md

Check only:
- `[BROKEN LINK]` — wikilinks in these files that don't resolve
- `[STALE INDEX]` — new files missing from index
- `[ORPHAN CONCEPT]` — new concepts not referenced by any chapter

If issues found, report them inline (do NOT auto-fix). This is not a full vault lint —
it catches only regressions introduced by this ingest.

## Pause Conditions

Only pause for user input when:
- Canonical resource already exists (ask before overwrite)
- Extraction candidates materially disagree
- Source appears corrupted
- A concept conflict changes course meaning

Do NOT pause for:
- Routine index updates
- Creating new concept/chapter pages
- Normal gap reporting
