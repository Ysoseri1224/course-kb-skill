# course-kb: Design Doc → Publishable Skill 改造指南

## 现状

`knowledge-base-skill-design.md` 是一份 1200+ 行的设计规范文档，定义了：
- 7+ 个命令（/ingest, /ingest-batch, /ingest-exam, /query, /lint, /status, /compare-extractions, /rebuild-index）
- vault 结构 schema
- frontmatter 规范
- 写作规则和不变量
- Obsidian 集成流程

**问题**：这不是一个可执行的 skill——它是设计文档。需要拆分、精简、格式化。

---

## 三种安装方式对比

| 方式 | 命令 | 适用场景 | 要求 |
|------|------|----------|------|
| `/install <github-url>` | 最简单 | 单 skill 快速分享 | repo 根目录有 `SKILL.md` |
| `/plugin marketplace add <url>` | 推荐 | 多 skill 套件 | `.claude-plugin/` 目录 + `skills/` 结构 |
| `npx` / npm install | 未实证 | 官方文档提及但无实际案例 | 暂不建议 |

---

## 方案 A：单 Skill（最快上线）

适合只发布 `/q` 核心功能的情况。

### 仓库结构
```
course-kb/
├── SKILL.md          ← 核心：skill 定义 + prompt
├── README.md         ← GitHub 展示
├── LICENSE           ← MIT
└── references/       ← 可选：参考文档
    └── vault-schema.md
```

### SKILL.md 格式
```yaml
---
name: course-kb
version: 1.0.0
description: |
  Course knowledge base skill for Obsidian vaults. Extracts course materials,
  indexes them as structured Markdown, and answers questions grounded only in
  vault content. Use when: user has a structured Obsidian vault with concepts/,
  chapters/, test/, resources/ directories and wants to generate exam practice
  materials or check vault health.
license: MIT
compatibility: claude-code
author: your-github-username
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
---

# course-kb

## Commands

### /q <question>
...prompt content...

### /lint
...prompt content...

### /status
...prompt content...
```

### 安装方式
```
/install https://github.com/yourname/course-kb
```
装完在 `~/.claude/skills/course-kb/` 下。

---

## 方案 B：Plugin 套件（推荐，功能完整）

适合发布完整工具链（ingest + query + lint + status）。

### 仓库结构
```
course-kb/
├── .claude-plugin/
│   ├── plugin.json         ← 插件元数据
│   └── marketplace.json    ← marketplace 注册信息
├── skills/
│   ├── q/
│   │   └── SKILL.md       ← /q 问答命令
│   ├── ingest/
│   │   └── SKILL.md       ← /ingest 提取命令
│   ├── lint/
│   │   └── SKILL.md       ← /lint 健康检查
│   └── status/
│       └── SKILL.md       ← /status 覆盖率
├── commands/
│   └── setup.md           ← /setup 初始化 vault 结构
├── references/
│   ├── vault-schema.md    ← vault 目录和 frontmatter 规范
│   ├── writing-rules.md   ← 写作不变量
│   └── exam-patterns.md   ← 考题模式识别规则
├── README.md
├── LICENSE
└── install.md             ← 安装后提示
```

### .claude-plugin/plugin.json
```json
{
  "$schema": "https://anthropic.com/claude-code/plugin.schema.json",
  "name": "course-kb",
  "version": "1.0.0",
  "description": "Course knowledge base toolkit — extract, index, and query course materials in Obsidian vaults. Generates exam practice files grounded only in vault content.",
  "author": {
    "name": "your-github-username",
    "url": "https://github.com/yourname"
  },
  "homepage": "https://github.com/yourname/course-kb",
  "repository": "https://github.com/yourname/course-kb",
  "license": "MIT",
  "keywords": ["obsidian", "knowledge-base", "exam", "study", "course", "rag"]
}
```

### .claude-plugin/marketplace.json
```json
{
  "$schema": "https://anthropic.com/claude-code/marketplace.schema.json",
  "name": "course-kb",
  "version": "1.0.0",
  "description": "Course knowledge base toolkit for Obsidian vaults",
  "owner": {
    "name": "your-github-username"
  },
  "plugins": [
    {
      "name": "course-kb",
      "version": "1.0.0",
      "source": "./",
      "description": "Extract course materials, build structured vaults, generate grounded exam practice.",
      "author": {
        "name": "your-github-username"
      },
      "keywords": ["obsidian", "knowledge-base", "exam", "study", "course"],
      "category": "academic"
    }
  ]
}
```

### 安装方式
```
/plugin marketplace add https://github.com/yourname/course-kb
/plugin install course-kb
```

---

## Design Doc 拆分方案

原 `knowledge-base-skill-design.md` 的内容应这样分配：

| 原文档章节 | 去向 | 理由 |
|-----------|------|------|
| §1-3 概述、目标、vault 结构 | `references/vault-schema.md` | skill 执行时的参考文档 |
| §4 frontmatter schema | `references/vault-schema.md` | 同上 |
| §5 写作规则和不变量 | `references/writing-rules.md` | /q 和 /ingest 引用 |
| §6 /ingest 命令定义 | `skills/ingest/SKILL.md` | 独立 skill |
| §7 /query 命令定义 | `skills/q/SKILL.md` | 独立 skill |
| §8 /lint 命令定义 | `skills/lint/SKILL.md` | 独立 skill |
| §9 /status 命令定义 | `skills/status/SKILL.md` | 独立 skill |
| §10 Obsidian 集成 | `README.md` + `install.md` | 用户文档 |
| §11 工具角色说明 | 删除或压缩到 README | 安装后不需要 |

### 每个 SKILL.md 的结构原则

1. **Frontmatter**：name, description（触发条件）, allowed-tools
2. **正文**：直接是可执行的 prompt，不超过 200 行
3. **引用 references/**：用 `See references/vault-schema.md for frontmatter spec` 引导 Claude 按需读取
4. **不重复**：共享规则放 references/，各 skill 引用而非复制

---

## 关键改造点

### 1. 从"规范文档"变成"可执行 prompt"

**Before（design doc 风格）**：
```
§7.2 The /query command accepts a natural language question and performs
structured retrieval across the vault index. The retrieval order is:
index → chapters → concepts → test → resources...
```

**After（SKILL.md 风格）**：
```
Answer the user's question: $ARGUMENTS

Strategy: progressive retrieval — stop when you can answer all parts.
Step 1: Read concepts/ files matching keywords.
Step 2: If needed, read test/ questions.
Step 3: Only for synthesis questions, read chapters/.
Max 5 files.

Output: write to practice/<date>-<topic>.md
...
```

### 2. 精简到可执行长度

每个 SKILL.md 正文控制在 **100-200 行**。超出的规则移到 references/ 让 Claude 按需读取。

### 3. 参数化设计

让 skill 适配不同课程，不硬编码 OPSMGT 741：
- vault 结构约定是通用的（concepts/, chapters/, test/, practice/）
- 课程名从 CLAUDE.md 或 vault frontmatter 读取
- 考题模式从 test/ 目录结构推断

---

## 发布 Checklist

- [ ] GitHub repo 创建（public）
- [ ] 选择方案 A 或 B
- [ ] 拆分 design doc 内容
- [ ] 写每个 SKILL.md（可执行 prompt 格式）
- [ ] 写 references/（共享规则文档）
- [ ] 写 README.md（使用说明 + 截图）
- [ ] 写 install.md（安装后引导）
- [ ] 本地测试：删除已有命令，/install 安装，验证 /q /lint /status 工作
- [ ] 发布：push to GitHub
- [ ] 验证安装：在另一台机器或新 vault 测试

---

## 下一步讨论

需要确认的决策：
1. **方案 A 还是 B？** — 只发 /q 还是全套工具链？
2. **ingest 命令是否保留？** — 目前 ingest 是你手动用 CLI 跑的，skill 用户是否也需要？
3. **是否参数化？** — 硬编码 OPSMGT 741 结构，还是做成通用模板？
4. **references/ 颗粒度** — vault-schema 和 writing-rules 是否够，还是需要更多？
