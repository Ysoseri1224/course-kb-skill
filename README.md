# course-kb-skill

[中文文档](./README.zh-CN.md) | English

A skill plugin that turns course materials into a structured Markdown knowledge base and answers questions grounded only in vault content.

Works with **Claude Code** and **OpenAI Codex CLI**.

## What it does

- **Extracts** course PDFs, slides, and documents into canonical Markdown resources
- **Builds** a structured vault with chapters (argument structure) and concepts (definitions)
- **Answers** questions with cited practice files grounded in vault evidence
- **Maintains** vault health with lint checks, index rebuilds, and incremental updates

## Commands

| Command | Purpose | Natural Language Trigger |
|---------|---------|------------------------|
| `build` | **Primary entry** — full guided setup + batch ingest | "帮我建知识库" / provide a folder |
| `q <question>` | **Daily use** — answer from knowledge base | Ask any course question |
| `ingest <file>` | Add one new file to existing vault | "帮我加入这个新讲义" |
| `ingest-batch <dir>` | Add a batch of new files | "这些新材料都加进去" |
| `ingest-exam <file>` | Extract exam questions | "这是往年考题" |
| `lint` | Check vault health | "检查一下知识库" |
| `status` | Coverage summary | "知识库现在什么情况" |
| `compare-extractions <file>` | Diagnostic: compare tools | "提取质量怎么样" |
| `rebuild-index` | Fix stale index | "重建索引" |

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

---

## Installation

### Claude Code

```
/plugin marketplace add https://github.com/Ysoseri1224/course-kb-skill
/plugin install course-kb
```

Then say "帮我建知识库" or run `/build` to start.

### OpenAI Codex CLI

**Option A — Install via Codex:**
```
Tell Codex: "Install the skill from github.com/Ysoseri1224/course-kb-skill"
```

**Option B — Manual install:**
```bash
git clone https://github.com/Ysoseri1224/course-kb-skill.git ~/.codex/skills/course-kb
```

Then restart Codex and say "帮我建知识库" or `/use build` to start.

**Option C — Project-level only:**
```bash
cp AGENTS.md /path/to/your/project/AGENTS.md
```

---

## Command Syntax Comparison

| Action | Claude Code | Codex CLI |
|--------|-------------|-----------|
| Build knowledge base | `/build` | `/use build` |
| Ask a question | `/q What is X?` | `/use q What is X?` |
| Add new file | `/ingest file.pdf` | `/use ingest file.pdf` |
| Check health | `/lint` | `/use lint` |
| Or just say... | "帮我建知识库" | "帮我建知识库" |

Both tools support natural language triggering — the description field in each SKILL.md tells the AI when to activate the skill automatically.

---

## Requirements

- Python 3.11+ (3.13 recommended)
- `uv` (Python package manager)
- **Claude Code** or **OpenAI Codex CLI**

### Recommended extraction tools

- [kb-extract](https://github.com/XUMAX-GH/kb-extract) — deterministic extraction
- [MinerU](https://github.com/opendatalab/MinerU) — layout-aware PDF extraction

`/build` will check tool availability and guide installation.

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
