---
name: build
version: 1.0.1
description: |
  Build a complete course knowledge base from source materials. This is the PRIMARY
  entry point for the skill. Use when the user says anything like:
  - "帮我建知识库" / "建一个课程知识库" / "把这门课整理成知识库"
  - "我有一门课的材料，帮我整理" / "这些是我的课件"
  - "build a knowledge base" / "create a course KB"
  - "I have course materials to organize"
  - User provides a folder of course files (PDFs, PPTs, etc.)
  - User mentions wanting to study/review a course systematically
  - .kb-config.md does not exist and user wants to work with course materials
  
  This skill runs the FULL pipeline: environment check → guided setup → batch ingest
  → vault built. The user only needs to answer a few questions, then everything runs
  automatically.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
  - PowerShell
---

# /build

Build a complete course knowledge base from source materials.

## User Experience

The user says something like "帮我把这门课建成知识库" or provides a folder path.
From there, this skill takes over: guided questions → automatic execution → done.

## Phase 1 — Environment Check (automatic, no user action needed)

Silently detect:
- Python 3.11+ (`py --version` or `python3 --version`)
- `uv` (`uv --version`)
- `kb-extract` (`kb --version`)
- `MinerU` (`mineru --version`)
- `.venv/` and packages (pymupdf4llm, python-pptx, markitdown)

If critical tools are missing, tell the user what's needed and offer to install.
Ask permission before installing anything. If user declines, note degraded capabilities
and continue with available tools.

## Phase 2 — Guided Setup (interactive, 3-5 questions max)

Ask the user these questions conversationally (not a wall of numbered lists):

1. **Where are your course materials?**
   "请告诉我你的课件文件夹路径（PDF、PPT、Word 都可以）"
   
2. **What's this course called?** (for naming, optional)
   "这门课叫什么？（用于文件命名，可以跳过）"

3. **Extraction tools** — only ask if multiple tools are available:
   "我检测到这些提取工具可用：[list]. 用推荐配置还是你想自己选？"

4. **Vault location** — only ask if non-default desired:
   "知识库默认建在 ./knowledge/，可以吗？"

5. **Do you have exam papers too?**
   "有历年考题吗？有的话一起告诉我路径，我会单独建考题库"

After answers: write `.kb-config.md`, create vault skeleton. No further confirmation needed.

## Phase 3 — Batch Ingest (fully automatic)

Run the full ingest pipeline on every supported file in the source directory:

```
For each file in source_dir (sorted by name):
  1. Extract with configured tools
  2. Compare extraction candidates
  3. Write canonical resource
  4. Analyze for chapters and concepts
  5. Write/update chapter pages
  6. Write/update concept pages
  7. Update index.md
```

Progress reporting: brief status per file (one line), not verbose logs.

```
[1/12] lecture-01-intro.pdf → ch01-introduction + 5 concepts ✓
[2/12] lecture-02-supply.pdf → ch02-supply-chain + 8 concepts ✓
[3/12] lecture-03-demand.pdf → ch03-demand-planning + 6 concepts ✓
...
```

**Pause ONLY when:**
- Extraction candidates materially disagree on a file
- Source file appears corrupted
- A concept conflict changes course meaning

**Do NOT pause for:** routine operations, new file creation, index updates.

## Phase 4 — Exam Ingest (if exam path provided)

If the user provided exam file paths in Phase 2:
- Run ingest-exam on each exam file
- File questions by topic
- Link to concept pages

## Phase 5 — Finalize

1. Run lint check (report issues, don't auto-fix)
2. Report summary:

```
知识库建设完成 ✓
─────────────────────────────
章节:        N chapters
概念:        N concepts  
资源:        N source files extracted
考题:        N questions (if any)
覆盖缺口:    N gaps identified

你现在可以用 /q <问题> 来查询知识库了。
新拿到讲义时，说"帮我加入新材料"即可追加。
```

## References

For detailed schemas and rules, read on demand:
- `references/vault-schema.md` — directory structure, frontmatter specs
- `references/writing-rules.md` — invariants, chapter/concept writing rules
- `references/exam-patterns.md` — exam question filing rules
