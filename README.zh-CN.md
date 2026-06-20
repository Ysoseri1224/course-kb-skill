# course-kb-skill

[English](./README.md) | 中文文档

将课程材料转化为结构化 Markdown 知识库，所有问答仅基于知识库内容生成。

支持 **Claude Code** 和 **OpenAI Codex CLI**。

## 功能

- **提取**：将课程 PDF、PPT、Word 文档转为标准 Markdown 资源文件
- **建库**：生成章节（论证结构）+ 概念（定义层）的结构化知识库
- **问答**：基于知识库证据生成带引用的练习文档
- **维护**：链接检查、索引重建、增量更新

## 典型使用场景

```
你有一门课的全部材料（PDF、PPT、讲义）
  → 说"帮我建知识库"，指定文件夹
  → 引导式确认几个问题后，全自动完成建库
  → 之后用"什么是 X""X 和 Y 有什么区别"随时查询
  → 新拿到讲义时，说"帮我加入这个新材料"追加
```

## 命令一览

| 命令 | 用途 | 自然语言触发 |
|------|------|-------------|
| `build` | **主入口** — 引导式建库（环境检查 + 批量提取） | "帮我建知识库" / 给一个文件夹路径 |
| `q <问题>` | **日常使用** — 从知识库回答问题 | 问任何课程相关问题 |
| `ingest <文件>` | 向已有知识库追加一个新文件 | "帮我加入这个新讲义" |
| `ingest-batch <目录>` | 批量追加新文件 | "这些新材料都加进去" |
| `ingest-exam <文件>` | 提取考题到题库 | "这是往年考题" |
| `lint` | 检查知识库健康状态 | "检查一下知识库" |
| `status` | 覆盖率概览 | "知识库现在什么情况" |
| `compare-extractions <文件>` | 诊断：对比提取工具输出 | "提取质量怎么样" |
| `rebuild-index` | 修复过期索引 | "重建索引" |

## 知识库结构

```
knowledge/
├── index.md          — 课程地图、概念索引、覆盖缺口
├── chapters/         — 章节论证结构（按讲次）
├── concepts/         — 概念定义（唯一权威来源）
├── resources/        — 提取后的标准资源文件（创建后只读）
├── test/             — 考题数据库
│   └── resources/    — 原始试卷（受保护，不可修改）
└── practice/         — 生成的练习文档（只增不改）
```

---

## 安装

### Claude Code

```
/plugin marketplace add https://github.com/Ysoseri1224/course-kb-skill
/plugin install course-kb
```

安装后说"帮我建知识库"或输入 `/build` 开始。

### OpenAI Codex CLI

**方式 A — 让 Codex 安装：**
```
告诉 Codex："Install the skill from github.com/Ysoseri1224/course-kb-skill"
```

**方式 B — 手动安装：**
```bash
git clone https://github.com/Ysoseri1224/course-kb-skill.git ~/.codex/skills/course-kb
```

重启 Codex 后说"帮我建知识库"或 `/use build` 开始。

**方式 C — 仅项目级：**
```bash
cp AGENTS.md /path/to/your/project/AGENTS.md
```

---

## 命令语法对比

| 操作 | Claude Code | Codex CLI |
|------|-------------|-----------|
| 建知识库 | `/build` | `/use build` |
| 问问题 | `/q 什么是X？` | `/use q 什么是X？` |
| 加新文件 | `/ingest file.pdf` | `/use ingest file.pdf` |
| 检查健康 | `/lint` | `/use lint` |
| 或者直接说... | "帮我建知识库" | "帮我建知识库" |

两个平台都支持自然语言触发——SKILL.md 中的 description 字段让 AI 自动识别用户意图并匹配对应命令。

---

## 环境要求

- Python 3.11+（推荐 3.13）
- `uv`（Python 包管理器）
- **Claude Code** 或 **OpenAI Codex CLI**

### 推荐提取工具

- [kb-extract](https://github.com/XUMAX-GH/kb-extract) — 确定性提取
- [MinerU](https://github.com/opendatalab/MinerU) — 版面感知 PDF 提取

`/build` 会自动检测工具可用性并引导安装。

## 设计原则

1. **本地 vault schema 是唯一权威** — 不让任何外部工具覆盖你的知识库规则
2. **每个源文件一份标准资源** — 多工具对比提取，最终只写一份
3. **章节 = 论证结构，概念 = 定义** — 职责严格分离
4. **不编造** — 知识库缺少的内容报告为缺口，不用通用知识填充
5. **增量友好** — 支持长期维护，不破坏已有内容

## Obsidian 集成

支持以下插件协作：
- **Claudian** — 从 Obsidian 内触发命令
- **Smart Connections** — 语义相关笔记推荐
- **Dataview** — 覆盖率仪表盘
- **Obsidian Git** — 版本检查点

## 许可证

MIT
