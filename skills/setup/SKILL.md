---
name: setup
version: 1.0.1
description: |
  Initialize course-kb configuration. Internal skill called by /build.
  Rarely used standalone — prefer /build for the full guided experience.
  Use standalone only when: reconfiguring tools, or .kb-config.md needs regeneration.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
  - PowerShell
---

# /setup

Initialize the course knowledge base configuration.

## Trigger

Run this command when:
- User explicitly asks to set up or initialize a course KB
- Any other command is invoked but `.kb-config.md` does not exist

## Behavior

This command is INTERACTIVE. All other commands are automatic after setup completes.

## Step 1 — Check local environment

Detect availability of these tools:

**Prerequisites:**
- `uv` — run `uv --version`
- Python 3.11+ — run `py --version` or `python3 --version`

**Extraction tools:**
- `kb-extract` — run `kb --version`
- `MinerU` — run `mineru --version`

**Python venv packages (check .venv/ existence and pip list):**
- pymupdf4llm
- python-pptx
- markitdown

**Optional tools:**
- claude-skill-markdown — check `.claude/skills/markdown/` existence

Report what is found and what is missing. For missing prerequisites, offer to install:
- uv: `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"` (Windows) or `curl -LsSf https://astral.sh/uv/install.sh | sh` (macOS/Linux)
- kb-extract: `uv tool install --from .tools/kb-extract kb-extract`
- MinerU: `uv tool install "mineru[all]"`
- venv: `py -3.13 -m venv .venv` then install packages

Ask user permission before installing anything.

## Step 2 — Confirm extraction tools

Present available extraction tools and ask user to select:

```
Available extraction tools:
1. kb-extract [found/missing]
2. MinerU [found/missing]
3. claude-skill-markdown [found/missing]
4. pymupdf4llm (PDF fallback) [found/missing]
5. python-pptx (PPTX fallback) [found/missing]
6. markitdown (DOCX fallback) [found/missing]

Recommended: kb-extract + MinerU
Which tools to use? (numbers, comma-separated)
```

## Step 3 — Confirm reference engine posture

```
Knowledge-base reference engines (design ideas only, not schema authorities):
1. nvk/llm-wiki — agentic wiki workflow ideas
2. kytmanov/synto — stable concept identity, incremental updates
3. axoviq-ai/synthadoc — contradiction/orphan/audit checks
4. Ar9av/obsidian-wiki — simple Obsidian wiki reference

Recommended: all as references, none controls schema
Accept recommended? (y/n)
```

## Step 4 — Confirm Obsidian plugins

```
Obsidian plugins for integration:
1. Claudian — trigger Claude workflows from Obsidian
2. Smart Connections — semantic note suggestions
3. Dataview — coverage dashboards
4. Obsidian Git — version checkpoints
5. Templater — file templates
6. QuickAdd — prompt capture
7. Commander — UI buttons
8. Copilot (optional) — sidebar chat

Recommended: 1-7
Which plugins are installed? (numbers, comma-separated)
```

## Step 5 — Confirm vault paths

```
Default vault paths:
  root:      ./knowledge
  chapters:  ./knowledge/chapters
  concepts:  ./knowledge/concepts
  resources: ./knowledge/resources
  test:      ./knowledge/test
  practice:  ./knowledge/practice

Accept defaults? (y/n, or provide custom paths)
```

## Step 6 — Write .kb-config.md

Write the configuration file with all confirmed selections.

## Step 7 — Create vault skeleton

If the vault root does not exist, create the directory structure:
```
knowledge/
├── index.md (empty template)
├── chapters/
├── concepts/
├── resources/
├── test/
│   └── resources/
└── practice/
```

## Step 8 — Confirm

Report:
- Configuration written to `.kb-config.md`
- Available commands: /ingest, /ingest-batch, /ingest-exam, /q, /lint, /status, /compare-extractions, /rebuild-index
- Any degraded capabilities due to missing tools
