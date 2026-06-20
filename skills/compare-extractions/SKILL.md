---
name: compare-extractions
version: 1.0.0
description: |
  Run extraction tools on a file and compare outputs without writing knowledge pages.
  Use when: testing extraction quality, diagnosing issues, or deciding if a file
  needs manual review.
allowed-tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
  - PowerShell
---

# /compare-extractions <file>

Run extraction tools and compare outputs without writing structured knowledge pages.

## Trigger

User wants to:
- Test a new source file type
- Diagnose extraction quality
- Decide whether a PDF needs OCR or manual review
- Compare MinerU vs kb-extract vs fallback output

## Procedure

1. Read `.kb-config.md` for configured tools.
2. Identify file type.
3. Run all configured extraction tools for that type.
4. Store outputs in `.extraction-cache/<slug>/`.
5. Compare candidates systematically.
6. Generate comparison report.

## Comparison Criteria

For each candidate pair, check:
- Page/slide count: do they agree?
- Text completeness: are major sections present in both?
- Heading hierarchy: consistent structure?
- Table quality: correctly formatted or corrupted?
- Speaker notes: present or missing?
- OCR/encoding: artifacts, garbled text, wrong characters?
- Large content blocks: appearing in only one candidate?

## Output Format

```
Extraction Comparison: <filename>
──────────────────────────────────
Tools used: <tool1>, <tool2>, ...

| Criterion       | <tool1>  | <tool2>  | Notes        |
|-----------------|----------|----------|--------------|
| Pages/slides    | N        | N        |              |
| Headings found  | N        | N        |              |
| Tables          | N ok     | N broken |              |
| Speaker notes   | yes/no   | yes/no   |              |
| OCR issues      | none/N   | none/N   |              |
| Missing blocks  | none/N   | none/N   |              |

Recommended canonical source: <tool> (reason)
Manual review needed: yes/no (reason)
```

## Rules

- Do NOT update chapters, concepts, or index
- Do NOT create canonical resource file
- Cache files in `.extraction-cache/` are temporary
- This is a diagnostic command only
