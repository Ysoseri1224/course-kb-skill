# course-kb-skill

A Claude Code plugin that turns course materials into a structured Markdown knowledge base and answers questions grounded only in vault content.

## What it does

- **Extracts** course PDFs, slides, and documents into canonical Markdown resources
- **Builds** a structured vault with chapters (argument structure) and concepts (definitions)
- **Answers** questions with cited practice files grounded in vault evidence
- **Maintains** vault health with lint checks, index rebuilds, and incremental updates

## Commands

| Command | Purpose |
|---------|---------|
| `/setup` | Initialize configuration, check tools, create vault skeleton |
| `/ingest <file>` | Extract and integrate one source file |
| `/ingest-batch <dir>` | Extract all files in a directory |
| `/ingest-exam <file>` | Extract exam questions into test database |
| `/q <question>` | Answer a question from the knowledge base |
| `/lint` | Check vault health (broken links, orphans, etc.) |
| `/status` | Show coverage summary |
| `/compare-extractions <file>` | Compare extraction tool outputs |
| `/rebuild-index` | Regenerate index.md from vault state |

## Vault Structure

```
knowledge/
├── index.md          — Course map, concept index, coverage gaps
├── chapters/         — Argument structure by lecture unit
├── concepts/         — Single source of truth for definitions
├── resources/        — Canonical extracted Markdown (read-only after creation)
├── test/             — Exam question database
│   └── resources/    — Original exam papers (protected)
└── practice/         — Generated answers and study guides (append-only)
```

## Installation

```
/plugin marketplace add https://github.com/Ysoseri1224/course-kb-skill
/plugin install course-kb
```

Then run `/setup` to initialize your vault.

## Requirements

- Claude Code
- Python 3.11+ (3.13 recommended)
- `uv` (Python package manager)

### Recommended extraction tools

- [kb-extract](https://github.com/XUMAX-GH/kb-extract) — deterministic extraction
- [MinerU](https://github.com/opendatalab/MinerU) — layout-aware PDF extraction

`/setup` will check tool availability and guide installation.

## Design Principles

1. **Local vault schema is the authority** — no external tool overrides your vault rules
2. **One canonical resource per source** — multiple extraction tools compare, one file wins
3. **Chapters = argument structure, Concepts = definitions** — strict separation
4. **No hallucination** — vault gaps are reported, not filled with general knowledge
5. **Incremental** — supports long-term maintenance without breaking existing content

## Obsidian Integration

Works with:
- **Claudian** — trigger commands from Obsidian
- **Smart Connections** — semantic note suggestions
- **Dataview** — coverage dashboards
- **Obsidian Git** — version checkpoints

## License

MIT
