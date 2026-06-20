# Course KB Skill — Project Instructions (Codex)

This is a course knowledge base skill. It turns course materials (PDFs, PPTs, documents) into a structured Markdown vault and answers questions grounded only in vault content.

## Skill Commands

- `/use build` — Primary entry: guided setup + full batch ingest
- `/use q` — Daily use: answer questions from the knowledge base
- `/use ingest` — Incremental: add a new file to existing vault
- `/use ingest-batch` — Add multiple new files
- `/use ingest-exam` — Extract exam questions
- `/use lint` — Check vault health
- `/use status` — Coverage summary
- `/use compare-extractions` — Diagnostic: compare extraction tools
- `/use rebuild-index` — Fix stale index

## Core Rules

- Local vault schema is the authority — no external tool overrides vault rules
- One canonical resource per source file
- Chapters = argument structure only, Concepts = definitions only
- Never fill gaps with general knowledge — report them
- resources/ is read-only after creation
- practice/ is append-only
- test/resources/ is never modified

## Vault Structure

```
knowledge/
├── index.md
├── chapters/
├── concepts/
├── resources/
├── test/
│   └── resources/
└── practice/
```

## Configuration

All settings live in `.kb-config.md`. Run `/use build` or `/use setup` to create it.
