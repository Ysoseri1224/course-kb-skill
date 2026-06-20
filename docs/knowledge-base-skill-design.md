---
name: course-kb
summary: Build and query a structured course knowledge base from source materials using a schema-governed Markdown vault.
version: 0.1.0
---

# Course KB Skill

## Required resources

Before using this skill, verify that configured resources exist locally or are otherwise available to the current Claude Code session. If a required resource is missing, first check local availability, then either install/download it when safe and authorized, or ask the user to confirm its location, installation status, or replacement.

### Local rule and configuration resources

| Resource | Required | Expected location or source | If missing |
|---|---:|---|---|
| Local vault rules | Yes | `CLAUDE.md` or project-equivalent instruction file | Ask the user to provide or confirm the vault rules before writing knowledge files. |
| Knowledge-base config | Yes after setup | `.kb-config.md` | Run `setup` before any ingest/query/lint/status command. |
| Vault root | Yes | Configured in `.kb-config.md`, default `knowledge/` | Create the vault skeleton only after setup confirms paths. |
| Chapter directory | Yes | `knowledge/chapters/` by default | Create if missing during vault initialization. |
| Concept directory | Yes | `knowledge/concepts/` by default | Create if missing during vault initialization. |
| Resource directory | Yes | `knowledge/resources/` by default | Create if missing; do not overwrite existing resources. |
| Test directory | Yes | `knowledge/test/` by default | Create if missing; protect `test/resources/`. |
| Practice directory | Yes | `knowledge/practice/` by default | Create if missing; treat as append-only. |

### Python virtual environment

All Python-based extraction libraries must be installed in a project-local venv. CLI tools (kb-extract, MinerU) are installed globally via `uv tool install`.

| Item | Path | Notes |
|---|---|---|
| venv location | `.venv/` (project root) | Created with `py -3.13 -m venv .venv` or equivalent |
| Activation (PowerShell) | `.venv\Scripts\Activate.ps1` | Required before running Python extraction code |
| Activation (bash) | `source .venv/bin/activate` | |
| Python version | 3.11+ required (3.13 recommended) | kb-extract requires 3.11+; use `py -3.13` on Windows if multiple versions installed |

**venv contains:** pymupdf4llm, python-pptx, markitdown[all], and their dependencies.

**CLI tools (global via uv):** `kb` (kb-extract), `mineru` (MinerU), `uv`.

When running Python extraction code in this skill, always use the venv Python:
```
.venv\Scripts\python.exe  (Windows)
.venv/bin/python          (macOS/Linux)
```

### Extraction resources

| Resource | Required | Official link | Role | Install / update guidance |
|---|---:|---|---|---|
| MinerU | Recommended for PDF | [GitHub](https://github.com/opendatalab/MinerU), [Docs](https://opendatalab.github.io/MinerU/) | Layout-aware PDF / slide extraction candidate | Install globally: `uv tool install "mineru[all]"`. Verify with `mineru --version`. |
| XUMAX-GH/kb-extract | Recommended | [GitHub](https://github.com/XUMAX-GH/kb-extract), [README](https://github.com/XUMAX-GH/kb-extract/blob/main/README.md) | Deterministic extraction candidate and reproducibility reference | Clone to `.tools/kb-extract`, then `uv tool install --from .tools/kb-extract kb-extract`. Verify with `kb --version`. |
| sarukas/claude-skill-markdown | Optional fallback | [GitHub](https://github.com/sarukas/claude-skill-markdown) | Broad-format Markdown conversion fallback | Install as Claude skill with `npx skill install sarukas/claude-skill-markdown`, or clone into `.claude/skills/markdown`. Update by reinstalling or pulling the skill repo. |
| `python-pptx` | Conditional | [PyPI](https://pypi.org/project/python-pptx/) | PPTX fallback preserving slide text and speaker notes | Install in venv: `.venv\Scripts\pip install -U python-pptx` |
| `markitdown` | Conditional | [PyPI](https://pypi.org/project/markitdown/), [GitHub](https://github.com/microsoft/markitdown) | DOCX / Office fallback conversion | Install in venv: `.venv\Scripts\pip install -U "markitdown[all]"` |
| `pymupdf4llm` | Conditional | [PyPI](https://pypi.org/project/pymupdf4llm/), [Docs](https://pymupdf.readthedocs.io/en/latest/pymupdf4llm/) | PDF fallback conversion when MinerU is unavailable | Install in venv: `.venv\Scripts\pip install -U pymupdf4llm` |
| `uv` | Prerequisite | [GitHub](https://github.com/astral-sh/uv), [Install docs](https://docs.astral.sh/uv/getting-started/installation/) | Python package/project manager, installs CLI tools globally | Windows: `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"`. macOS/Linux: `curl -LsSf https://astral.sh/uv/install.sh | sh`. |

If any recommended extraction resource is missing, ask before installing. If installation is not authorized, continue only with available configured tools and record degraded extraction confidence.

### Reference-engine resources

These are references for workflow design and quality checks. They are not required to write final vault files unless the user explicitly configures them as available tools.

| Resource | Required | Official link | Role | Install / update guidance |
|---|---:|---|---|---|
| nvk/llm-wiki | Optional reference | [GitHub](https://github.com/nvk/llm-wiki), [Site](https://llm-wiki.net/) | Agentic wiki workflow reference | Claude Code plugin install is documented as `claude plugin install wiki@llm-wiki`. Update through Claude plugin tooling or by following the GitHub README. Proceed without it if unavailable. |
| kytmanov/synto | Optional reference | [GitHub](https://github.com/kytmanov/synto), [PyPI](https://pypi.org/project/synto/) | Stable concept identity and incremental update reference | Install/update from PyPI if explicitly used; otherwise borrow the design idea only. Do not let synto control final vault schema. |
| axoviq-ai/synthadoc | Optional reference | [GitHub](https://github.com/axoviq-ai/synthadoc), [Quick start](https://github.com/axoviq-ai/synthadoc/blob/main/docs/user-quick-start-guide.md) | Contradiction/orphan/audit reference | Use documentation as reference unless explicitly configured. Check license and project maturity before adopting directly. |
| Ar9av/obsidian-wiki | Optional reference | [GitHub](https://github.com/Ar9av/obsidian-wiki), [README](https://github.com/Ar9av/obsidian-wiki/blob/main/README.md), [SETUP](https://github.com/Ar9av/obsidian-wiki/blob/main/SETUP.md) | Simple Obsidian wiki workflow reference | Install is documented as `pip install obsidian-wiki` or clone the repo. Use as reference only unless explicitly selected. |

### Obsidian integration resources

These are required only for Obsidian-side interaction. Core Claude Code ingestion and query commands must still work without them.

Install/update general rule: use Obsidian `Settings -> Community plugins -> Browse` to install by name; use `Community plugins -> Check for updates` to update installed plugins. If a plugin is not in the local Obsidian plugin browser, use the linked GitHub repository only after user confirmation.

| Resource | Required | Official link | Role | Install / update guidance |
|---|---:|---|---|---|
| Claudian | Optional integration | [GitHub](https://github.com/YishenTu/claudian), [Community plugin](https://community.obsidian.md/plugins/realclaudian) | Trigger Claude/Claude Code workflows from Obsidian | Install from Obsidian Community Plugins when possible. Use only when Obsidian-side triggering is needed. |
| Smart Connections | Optional integration | [GitHub](https://github.com/brianpetro/obsidian-smart-connections), [Community plugin](https://community.obsidian.md/plugins/smart-connections), [Docs](https://smartconnections.app/) | Suggest relevant vault pages for queries | Install from Community Plugins. If missing, continue with direct file reads. |
| Dataview | Optional integration | [GitHub](https://github.com/blacksmithgu/obsidian-dataview), [Community plugin](https://community.obsidian.md/plugins/dataview), [Docs](https://blacksmithgu.github.io/obsidian-dataview/) | Dashboards and coverage views | Install from Community Plugins. If missing, continue without dashboards. |
| Obsidian Git | Optional integration | [GitHub](https://github.com/Vinzent03/obsidian-git), [Community plugin](https://community.obsidian.md/plugins/obsidian-git) | Version checkpoints and rollback safety | Install from Community Plugins. If missing, recommend manual backup/checkpoint before large ingests. |
| Templater | Optional integration | [GitHub](https://github.com/SilentVoid13/Templater), [Community plugin](https://community.obsidian.md/plugins/templater-obsidian) | Obsidian-side templates | Install from Community Plugins. If missing, use the skill's built-in Markdown schemas. |
| QuickAdd | Optional integration | [GitHub](https://github.com/chhoumann/quickadd), [Community plugin](https://community.obsidian.md/plugins/quickadd), [Docs](https://quickadd.obsidian.guide/docs/) | Prompt capture and workflow macros | Install from Community Plugins. If missing, use Claude Code commands directly. |
| Commander | Optional integration | [Community plugin](https://community.obsidian.md/plugins/cmdr), [GitHub](https://github.com/phibr0/obsidian-commander) | Obsidian UI buttons/menu entries | Install from Community Plugins. If missing, use command palette or Claude Code commands. |
| Copilot for Obsidian | Optional integration | [GitHub](https://github.com/logancyang/obsidian-copilot), [Website](https://www.obsidiancopilot.com/) | Optional sidebar chat | Install only if sidebar chat is desired. Never treat Copilot output as authoritative structured knowledge. |

### Source-material resources

For each ingest or query command, verify the specific source material exists locally before processing.

| Resource | Required | Examples | If missing |
|---|---:|---|---|
| Course source files | Required for ingest | PDF, PPTX, DOCX, Markdown | Ask the user for the correct path. Do not guess filenames or URLs. |
| Exam source files | Required for ingest-exam | Past papers, quizzes, tests | Ask the user for the correct path. Do not fabricate questions. |
| Existing vault pages | Required for query | `index.md`, chapters, concepts, tests | If absent, state that the knowledge base lacks enough material. |

### Resource acquisition rule

When a required tool or source is missing:

1. Prefer local discovery first.
2. Use official links above for install/update guidance.
3. If installation, download, or plugin setup changes the local system, ask for authorization first.
4. If the missing item is course material, ask the user for the file path; do not search the web or guess URLs.
5. If an optional integration is missing, continue with the core Claude Code workflow where possible.
6. Record degraded behavior in the command output when a fallback path is used.

This skill turns course materials into a structured Markdown knowledge base and answers questions from that knowledge base.

It is designed for courses where the user wants a controlled vault structure, not a generic resource search system. The skill follows the local vault rules as the authority and uses extraction tools, knowledge-base engines, and Obsidian plugins only as supporting components.

The skill has two core capabilities:

1. **Ingest course materials** into a structured Markdown vault.
2. **Answer questions from the vault** and write grounded Markdown outputs.

---

## 1. When to use this skill

Use this skill when the user wants to:

- Convert course PDFs, slides, readings, handouts, or exam papers into a structured Markdown knowledge base.
- Maintain a course vault with chapters, concepts, resources, tests, and generated practice documents.
- Use Claude Code or Codex to follow explicit vault rules while organizing course material.
- Ask questions against the existing knowledge base and generate cited Markdown answers.
- Use Obsidian as the reading, querying, or writing interface.
- Compare multiple extraction tools instead of trusting one parser.
- Incrementally update a course knowledge base over time.

Do not use this skill for:

- General web research.
- Generic note summarization without a vault schema.
- Free-form chat over files without writing structured Markdown.
- Replacing the user's existing vault structure with a third-party tool's structure.
- Producing answers from general model knowledge when the vault lacks evidence.（unless permitted by user）

---

## 2. Core principle

The local vault schema is the authority.

External tools are supporting inputs only.

```text
Local vault rules
  control final structure, invariants, and write behavior.

Extraction tools
  provide candidate readings of source files.

Reference knowledge-base tools
  provide useful design ideas, not final schemas.

Obsidian plugins
  provide UI, retrieval, commands, templates, and version safety.

Claude Code
  performs comparison, reasoning, writing, verification, and maintenance.
```

The skill must never let an extraction tool or knowledge-base engine override the local `CLAUDE.md`, `.kb-config.md`, or equivalent vault instructions.

---

## 3. Required vault model

The skill expects a structured Markdown vault. The exact local schema may differ by project, but this course uses:

```text
knowledge/
├── CLAUDE.md
├── .kb-config.md
├── index.md
├── chapters/
├── concepts/
├── test/
├── resources/
└── practice/
```

Default meanings:

| Path | Purpose |
|---|---|
| `knowledge/index.md` | Course overview, chapter map, concept index, coverage gaps |
| `knowledge/chapters/` | Argument structure by chapter or lecture unit |
| `knowledge/concepts/` | Single source of truth for concept definitions |
| `knowledge/resources/` | Canonical extracted source Markdown; evidence layer only |
| `knowledge/test/` | Exam and test question database |
| `knowledge/test/resources/` | Original exam papers; protected from Claude writes |
| `knowledge/practice/` | Generated answers, study guides, practice documents |

The skill must read the local project rules before writing, because the current course may customize names, schemas, or invariants.

---

## 4. Invariants

Unless the local project rules explicitly override them, enforce these invariants:

1. `chapters/` contains argument structure only.
2. `concepts/` is the single source of truth for definitions.
3. `resources/` contains canonical extraction artifacts and is read-only after creation.
4. `test/resources/` contains original exam files and is never modified by Claude.
5. `practice/` is append-only; create new files rather than editing old ones.
6. Every concept mentioned in a chapter must be wikilinked.
7. Every chapter mentioned in a concept must be wikilinked.
8. Do not silently overwrite existing knowledge.
9. Do not silently resolve contradictions between sources.
10. Do not fill course knowledge gaps with general model knowledge.
11. All substantive generated answers must cite structured vault pages.
12. Raw resources are evidence, not the primary learning interface.

---

## 5. Configuration

Before running any ingest, query, lint, or status command, read `.kb-config.md` if it exists.

If `.kb-config.md` does not exist, run setup.

Recommended configuration for this course:

```markdown
# Knowledge Base Configuration
generated: 2026-06-17

## extraction
tools: [kb-extract, MinerU]
optional: [claude-skill-markdown]
# routing:
# pdf:  MinerU + kb-extract
# pptx: kb-extract + claude-skill-markdown
# docx: kb-extract + claude-skill-markdown

## controller
primary: claude-code-skill
schema-authority: ./CLAUDE.md

## kb-engine-reference
primary-reference: nvk/llm-wiki
borrowed-patterns: [synto incremental concepts, synthadoc contradiction-orphan-audit]
direct-output-controls-schema: false

## obsidian-plugins
installed: [Claudian, Smart Connections, Dataview, Obsidian Git, Templater, QuickAdd, Commander]
optional: [Copilot]

## vault
root:      ./knowledge
chapters:  ./knowledge/chapters
concepts:  ./knowledge/concepts
resources: ./knowledge/resources
test:      ./knowledge/test
practice:  ./knowledge/practice

## behavior
multi-extraction: true
canonical-resource-only: true
resources-read-only-after-creation: true
practice-append-only: true
allow-general-knowledge-as-course-content: false
```

---

## 6. Tool roles

### 6.1 Extraction tools

Use extraction tools to produce candidate text, not final knowledge pages.

Recommended extraction set:

```text
kb-extract + MinerU
```

Optional fallback:

```text
claude-skill-markdown
```

| Tool | Role |
|---|---|
| MinerU | PDF and complex-layout extraction candidate |
| kb-extract | Deterministic/reproducible extraction candidate |
| claude-skill-markdown | Optional broad-format fallback or third comparison candidate |

The skill should compare extraction candidates and write one canonical resource file.

### 6.2 Knowledge-base engines

Knowledge-base engines are reference systems, not schema authorities.

Recommended posture:

| Tool | Use |
|---|---|
| nvk/llm-wiki | Main reference for Claude/agentic wiki workflow ideas |
| kytmanov/synto | Borrow stable concept identity and incremental update ideas |
| axoviq-ai/synthadoc | Borrow contradiction, orphan, and audit checks |
| Ar9av/obsidian-wiki | Optional simple Obsidian-wiki reference |

Do not accept any engine's generated structure if it conflicts with the local vault schema.

### 6.3 Obsidian plugins

Recommended plugins:

```text
Claudian
Smart Connections
Dataview
Obsidian Git
Templater
QuickAdd
Commander
```

Optional:

```text
Copilot for Obsidian
```

| Plugin | Role |
|---|---|
| Claudian | Trigger Claude/Claude Code workflows from Obsidian |
| Smart Connections | Suggest relevant vault pages for queries |
| Dataview | Build coverage tables and dashboards |
| Obsidian Git | Provide checkpoints and rollback safety |
| Templater | Provide file templates |
| QuickAdd | Capture prompts and trigger workflows |
| Commander | Expose commands in Obsidian UI |
| Copilot | Optional sidebar chat, not authoritative writing |

---

## 7. Commands

The skill supports these command intents. They may be implemented as namespaced commands or mapped to local slash commands.

```text
/course-kb setup
/course-kb ingest <file>
/course-kb ingest-batch <dir>
/course-kb ingest-exam <file>
/course-kb query <question>
/course-kb lint
/course-kb status
/course-kb compare-extractions <file>
/course-kb rebuild-index
```

Equivalent short commands may be used in a project:

```text
/setup
/ingest <file>
/ingest-batch <dir>
/ingest-exam <file>
/query <question>
/lint
/status
```

---

# Command: setup

## Purpose

Create `.kb-config.md` by confirming the extraction tools, reference engines, and Obsidian plugins that the workflow will use.

## Behavior

Setup is interactive. All other commands should be automatic after setup.

## Steps

1. Verify candidate tools exist and are usable.
2. Exclude unconfirmed tools.
3. Ask the user to select extraction tools.
4. Ask the user to select the knowledge-base engine posture or reference engine.
5. Ask the user to select Obsidian plugins.
6. Write `.kb-config.md`.
7. Confirm available commands.

## Recommended default choices

```text
Extraction: kb-extract + MinerU
Reference engine: nvk/llm-wiki, with synto/synthadoc ideas borrowed only
Obsidian plugins: Claudian, Smart Connections, Dataview, Obsidian Git, Templater, QuickAdd, Commander
Optional: Copilot
```

## Output

```text
.kb-config.md
```

---

# Command: ingest <file>

## Purpose

Ingest one course source file into the structured knowledge base.

Input may be:

- PDF lecture slides.
- PPTX slide deck.
- DOCX reading or handout.
- Markdown source.
- Other configured file type.

## Full behavior

```text
source file
  -> run configured extraction tools
  -> compare extraction candidates
  -> produce canonical resource Markdown
  -> survey existing vault
  -> analyze resource
  -> write/update chapters
  -> write/update concepts
  -> update index
  -> verify invariants
```

## Step 1 — Preflight

Before extracting:

1. Read local `CLAUDE.md` or equivalent rules.
2. Read `.kb-config.md`.
3. Verify the source file exists.
4. Identify file type.
5. Determine configured extraction tools.
6. Confirm target vault paths.
7. Check whether the canonical resource already exists.

If the canonical resource already exists, do not overwrite it unless the user explicitly asks for re-extraction.

## Step 2 — Multi-extraction

Run all configured extraction tools for the file type.

Examples:

```text
PDF:
- MinerU
- kb-extract
- optional claude-skill-markdown

PPTX:
- kb-extract
- optional claude-skill-markdown
- direct python-pptx fallback preserving speaker notes

DOCX:
- kb-extract
- optional claude-skill-markdown
- markitdown fallback if configured
```

Extraction candidates may be stored temporarily or in a hidden cache such as:

```text
.extraction-cache/<source-slug>/mineru.md
.extraction-cache/<source-slug>/kb-extract.md
.extraction-cache/<source-slug>/skill-markdown.md
.extraction-cache/<source-slug>/comparison.md
```

Do not treat this cache as the final knowledge base.

## Step 3 — Compare extraction candidates

Compare candidates for:

- Page or slide count mismatch.
- Major missing text.
- Heading hierarchy differences.
- Table corruption.
- Speaker note presence or absence.
- OCR uncertainty.
- Encoding artifacts.
- Large content blocks appearing in only one candidate.

If differences are minor, choose or merge the best candidate automatically.

Pause and ask the user only if:

- One extraction is clearly incomplete.
- Tables or figures contain likely exam-relevant content but are corrupted.
- OCR ambiguity changes meaning.
- Source file appears damaged.
- Candidate outputs disagree so much that canonical resource creation would be unsafe.

## Step 4 — Create canonical resource

Write exactly one public resource file:

```text
knowledge/resources/<source-slug>.md
```

Canonical resource rules:

- Preserve source order.
- Preserve page or slide markers.
- Preserve titles, headings, bullet structure, and tables where possible.
- Preserve speaker notes when available.
- Do not summarize.
- Do not define concepts.
- Do not insert outside knowledge.
- Do not write multiple competing resource files into `resources/`.

## Step 5 — Survey existing vault

Before writing chapters or concepts:

1. Read `knowledge/index.md`.
2. Read chapter filenames, frontmatter, and headings.
3. Read concept filenames.
4. Read related concept pages if likely affected.
5. Identify existing chapter sequence.
6. Identify existing coverage gaps.

## Step 6 — Analyze source

Analyze the canonical resource for:

1. Which chapter or chapters it belongs to.
2. Whether new chapters are needed.
3. Every new concept introduced.
4. Existing concepts reinforced or contradicted.
5. Lecturer's argumentative structure.
6. Repeated or emphasized terms.
7. Exam signals.
8. Examples and case studies.
9. Connections to other chapters.
10. Open questions or unresolved tensions.

## Step 7 — Write chapter pages

Chapter pages must follow the local schema.

Default schema:

```markdown
---
title: "<Chapter Title>"
sequence: <N>
sources: ["[[resources/<source-slug>]]"]
updated: <YYYY-MM-DD>
---

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

Rules:

- No inline concept definitions.
- Use wikilinks for all concepts.
- Write logical argument structure, not slide-by-slide notes.
- Add `## Conflicts` if a new source contradicts existing chapter claims.
- Preserve existing material unless explicitly updating with attribution.

## Step 8 — Write concept pages

Concept pages must follow the local schema.

Default schema:

```markdown
---
title: "<Concept Name>"
aliases: []
chapters: ["[[chapters/chNN-xxx]]"]
sources: ["[[resources/<source-slug>]]"]
updated: <YYYY-MM-DD>
---

# <Concept Name>

## Definition

## Core Arguments

## Relationship to Other Concepts

## Lecturer's Emphasis

## Examples & Cases

## Exam Notes
```

Rules:

- Concept pages are the only place for definitions.
- Examples and cases belong in concept pages.
- Exam signals belong in concept pages.
- If a concept already exists, append or update carefully; do not overwrite.
- If sources conflict, note the conflict explicitly.
- Update frontmatter sources and chapter links.

## Step 9 — Update index

Update `knowledge/index.md` with:

- Chapter map.
- Concept index.
- Coverage gaps.
- Updated metadata.

Rules:

- Every concept file appears in the concept index.
- Every chapter file appears in the chapter map.
- Gaps stay visible until resolved by source evidence.

## Step 10 — Verify

Before reporting completion, verify:

- All new wikilinks resolve.
- Every new concept appears in the index.
- Every new chapter appears in the index.
- No chapter contains inline concept definitions.
- Resource file exists.
- Referenced sources exist.
- Concept pages link to relevant chapters.
- Chapters link to relevant concepts.
- Protected directories were not modified.
- Existing practice files were not edited.

---

# Command: ingest-batch <dir>

## Purpose

Ingest all supported files in a directory sequentially.

## Behavior

1. Read config.
2. Identify supported files.
3. Sort files in stable order.
4. Run `ingest <file>` on each file.
5. Run `lint` at the end.
6. Report summary.

## Summary format

```text
Batch complete.
─────────────────────────────
Files processed:       N
New resources created: N
New chapters created:  N
New concepts created:  N
Concepts updated:      N
Broken links:          N
Coverage gaps:         N
```

## Rules

- Do not skip files silently.
- If a file fails extraction, record it and continue only when safe.
- Do not overwrite existing canonical resources without explicit permission.

---

# Command: ingest-exam <file>

## Purpose

Ingest a past exam, test, quiz, or assessment paper.

## Behavior

Exam ingestion is separate from normal content ingestion.

## Steps

1. Extract or transcribe questions faithfully.
2. Preserve original wording.
3. Identify question type.
4. Identify topic or concept links.
5. Identify whether the question is single-topic or cross-topic.
6. Write question entries.
7. Update `test/index.md`.
8. Update relevant concept pages under `## Exam Notes`.

## Critical rule

Do not fabricate answers.

If no answer key is present, the answer section stays blank.

## Default question schema

```markdown
## Q<year>-<number>

**Type:** MCQ | Short Answer | Essay | Calculation | Other
**Topics:** [[concepts/concept-a]], [[chapters/chNN-xxx]]
**Source:** [[test/resources/<exam-filename>]]
**Year:** <YYYY>

### Question
<exact question text>

### Options
- A. ...
- B. ...
- C. ...
- D. ...

### Answer

### Analysis
```

## Filing rules

```text
Single-topic question:
  knowledge/test/<topic-name>/questions.md
  knowledge/test/<topic-name>/by-type/<type>.md

Cross-topic question:
  knowledge/test/cross-topic/questions.md
  knowledge/test/cross-topic/by-type/<type>.md
```

---

# Command: query <question>

## Purpose

Generate a Markdown answer from the existing knowledge base.

Input may be:

- A typed question.
- A long paragraph.
- Selected text from Obsidian.
- Cursor-adjacent context.
- A request for exam-format response.

## Default output

Create a new file:

```text
knowledge/practice/<yyyy-mm-dd>-<topic-slug>.md
```

Do not edit existing practice files unless explicitly instructed.

## Step 1 — Parse query

Classify the query type:

```text
definition
comparison
argument
exam-format answer
study guide
case application
concept map
gap analysis
```

Identify likely chapters, concepts, and output style.

## Step 2 — Retrieve from vault

Read sources in this order:

1. `knowledge/index.md`
2. Relevant `knowledge/chapters/*.md`
3. Relevant `knowledge/concepts/*.md`
4. Relevant `knowledge/test/**/*.md` if exam-related
5. `knowledge/resources/*.md` only when structured pages explicitly need source detail

Smart Connections or similar semantic tools may suggest relevant notes, but the skill must read the actual Markdown pages before answering.

## Step 3 — Generate answer

Generate an answer appropriate to the query type.

Rules:

- Ground every substantive claim in the vault.
- Cite chapter, concept, or test pages.
- Use resource pages only when necessary.
- Do not present outside knowledge as course content.
- If the vault lacks information, say so.

## Step 4 — Write practice file

Default schema:

```markdown
---
query: "<original question>"
date: <YYYY-MM-DD>
sources: ["[[chapters/chNN-xxx]]", "[[concepts/concept-a]]"]
---

# <Descriptive Title>

## Answer

## Knowledge Base References
- <claim>: [[concepts/concept-a#Core Arguments]]
- <claim>: [[chapters/chNN-xxx#Key Claims & Emphases]]

## Gaps & Uncertainties

## Related Exam Questions
```

## Gap behavior

If the answer requires missing knowledge:

1. State the gap in `## Gaps & Uncertainties`.
2. Add the gap to `knowledge/index.md` under `## Coverage Gaps` if required by local rules.
3. Do not invent course-specific content.

## Insert-at-cursor mode

If the user explicitly requests insertion into an existing note:

1. Read the target note first.
2. Preserve existing content.
3. Insert only the generated section.
4. Include citations.
5. Do not overwrite user-authored text.

Default remains new practice file.

---

# Command: compare-extractions <file>

## Purpose

Run extraction tools and compare outputs without writing structured knowledge-base pages.

## Use when

- Testing a new source type.
- Diagnosing extraction quality.
- Deciding whether a PDF needs OCR/manual review.
- Comparing MinerU vs kb-extract vs fallback output.

## Output

A comparison report covering:

```text
- Candidate tools used
- Page/slide counts
- Missing major sections
- Table quality
- Speaker note presence
- OCR or encoding problems
- Recommended canonical source
- Manual review warnings
```

This command may write a temporary or hidden comparison report, but it should not update chapters, concepts, or index.

---

# Command: lint

## Purpose

Check vault health without auto-fixing by default.

## Checks

Report these issue types:

```text
[BROKEN LINK]      A wikilink points to a missing file.
[ORPHAN CONCEPT]   A concept is not referenced by any chapter.
[ORPHAN CHAPTER]   A chapter is not listed in index.md.
[INLINE DEF]       A chapter appears to define a concept inline.
[EMPTY SECTION]    A required section is empty.
[MISSING SOURCE]   A source link points to a missing resource.
[CONFLICT]         Sources disagree on a concept or claim.
[EXAM NO ANSWER]   Question has no answer key; informational only.
[STALE INDEX]      File exists but index.md does not list it.
[PROTECTED WRITE]  Protected directory or append-only file was modified.
```

## Output format

```text
[BROKEN LINK] chapters/ch01-example.md -> [[concepts/missing-concept]]
[ORPHAN CONCEPT] concepts/example.md — not referenced by any chapter
[INLINE DEF] chapters/ch02-example.md — possible inline definition detected
```

## Rule

Do not repair automatically unless the user asks for repair.

---

# Command: status

## Purpose

Summarize current knowledge-base coverage and configuration.

## Output format

```text
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
Extraction:      <tools>
Reference:       <engine posture>
```

---

# Command: rebuild-index

## Purpose

Rebuild `knowledge/index.md` from the current chapters and concepts.

## Behavior

1. Read all chapter pages.
2. Read all concept pages.
3. Preserve existing coverage gaps unless clearly resolved.
4. Regenerate chapter map.
5. Regenerate concept index.
6. Update metadata.
7. Verify all listed links resolve.

## Rule

Do not erase manually written notes in index unless the local schema says the index is fully generated.

---

## 8. Writing rules

### 8.1 General writing rules

When writing knowledge pages:

- Prefer precise course-local language.
- Preserve source attribution.
- Use wikilinks consistently.
- Keep chapter and concept responsibilities separate.
- Avoid generic textbook filler unless present in the course materials.
- Make conflicts and gaps visible.

### 8.2 Chapter writing rules

A chapter page should answer:

- What argument is the lecturer building?
- Why does this topic matter in the course?
- Which concepts are central?
- What claims or emphases are exam-relevant?
- How does this chapter connect to other chapters?

A chapter page should not answer:

- What is the complete definition of each concept?
- What are all examples and cases?

Those belong in concept pages.

### 8.3 Concept writing rules

A concept page should answer:

- How does this course define the concept?
- What claims does the lecturer make about it?
- Which concepts is it related to or contrasted with?
- What examples illustrate it?
- How might it appear in exams?

### 8.4 Practice writing rules

A practice document should:

- Answer the user's query.
- Use a register appropriate to the task.
- Cite knowledge-base references.
- State gaps explicitly.
- Link related exam questions when available.

---

## 9. Incremental maintenance

The skill must support long-term incremental maintenance.

When adding a new source:

1. Do not rename existing concepts unnecessarily.
2. Do not duplicate concepts under slightly different slugs.
3. Append new source perspectives to existing concepts.
4. Add conflicts when sources disagree.
5. Update `sources:` frontmatter.
6. Update `updated:` dates.
7. Update index coverage.
8. Run lint.

Stable slug rules:

- Lowercase.
- Hyphen-separated.
- Deterministic.
- Preserve course numbering where available.
- Do not rename existing files without explicit user approval.

Optional future enhancement:

- Add stable `entity_id` to concept frontmatter if concept drift becomes difficult to manage.

---

## 10. Obsidian workflow

### 10.1 Query from Obsidian

Expected workflow:

```text
User types or selects question in Obsidian
  -> QuickAdd or Commander triggers query
  -> Claudian sends query to Claude Code
  -> Smart Connections optionally suggests relevant notes
  -> Claude Code reads structured vault pages
  -> Claude Code writes new practice Markdown
  -> User reviews result in Obsidian
```

### 10.2 Ingest from Obsidian

Expected workflow:

```text
User places source files in a source folder
  -> Commander or Claude Code command triggers ingest-batch
  -> Extraction candidates are generated
  -> Canonical resources are created
  -> Chapters/concepts/index/test are updated
  -> Lint runs
  -> Obsidian Git provides checkpointing if configured
```

### 10.3 Dashboards

Dataview can be used to create:

- Chapter coverage table.
- Concept index table.
- Concepts with empty sections.
- Sources not yet ingested.
- Practice answers by date/topic.
- Exam questions by concept/type.
- Coverage gaps dashboard.

---

## 11. Human review checkpoints

Most commands should be automatic after setup. Pause only when the decision genuinely belongs to the user.

Pause when:

- Extraction candidates materially disagree.
- Existing resource replacement would be required.
- A source appears corrupted.
- A concept conflict changes course meaning.
- The insertion target is ambiguous.
- Configured tools are missing.
- The local schema conflicts with requested output.

Do not pause for:

- Routine index updates.
- Creating new concept pages.
- Creating new chapter pages from clear source material.
- Creating new practice files.
- Reporting normal gaps.

---

## 12. Failure behavior

When a command cannot complete:

1. State what failed.
2. State which files, if any, were written.
3. State whether the vault is still consistent.
4. Do not delete or overwrite files to recover unless the user explicitly approves.
5. If partial writes occurred, run or recommend `lint`.
6. Keep original source and extraction candidates available where possible.

---

## 13. Recommended defaults for this course

Use these defaults unless the user chooses otherwise:

```text
Extraction:
- kb-extract
- MinerU
- claude-skill-markdown as fallback only

Reference posture:
- nvk/llm-wiki as workflow reference
- synto ideas for stable/incremental concept maintenance
- synthadoc ideas for contradiction/orphan/audit checks
- no external engine controls the final schema

Obsidian:
- Claudian
- Smart Connections
- Dataview
- Obsidian Git
- Templater
- QuickAdd
- Commander
- Copilot optional

Write behavior:
- One canonical resource per source
- resources read-only after creation
- practice append-only
- query outputs default to new practice files
- insert-at-cursor only by explicit request
```

---

## 14. Acceptance criteria

The skill is working when:

- Setup produces `.kb-config.md`.
- A source file can be converted into one canonical resource.
- Multiple extraction candidates can be compared.
- Chapters are written with argument structure and no concept definitions.
- Concepts are written as the definition and example layer.
- Index lists all chapters and concepts.
- Queries produce cited practice Markdown files.
- Missing course evidence is reported as a gap.
- Lint reports broken links, orphan pages, missing sources, empty sections, and possible inline definitions.
- Existing resources and practice files are not overwritten accidentally.
- The same workflow can handle later incremental course materials.

---

## 15. Quick-start recommendation

If the user accepts the recommended setup, use:

```text
Extraction: 2 3
Knowledge-base reference: 4
Obsidian plugins: 1 2 6 11 12 13 17
Optional plugin: 3
```

Plain-language version:

```text
Use kb-extract + MinerU for extraction.
Use llm-wiki as workflow reference only.
Borrow synto and synthadoc design ideas.
Use Claudian, Smart Connections, Dataview, Obsidian Git, Templater, QuickAdd, and Commander for Obsidian integration.
Copilot is optional for sidebar chat.
```
