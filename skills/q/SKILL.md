---
name: q
version: 1.0.0
description: |
  Answer questions from the course knowledge base. Use when: user asks a question
  about course content, requests exam practice, study guide, concept explanation,
  or comparison. Generates a cited Markdown practice file grounded in vault content.
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
---

# /q <question>

Answer the user's question based on the course knowledge base.

## Trigger

User asks a question about course content, requests:
- Concept definition or explanation
- Comparison between concepts
- Argument analysis
- Exam-format answer
- Study guide
- Case application
- Concept map
- Gap analysis

## Output

Create a new file: `knowledge/practice/<yyyy-mm-dd>-<topic-slug>.md`

Do NOT edit existing practice files unless user explicitly requests insert-at-cursor.

## Step 1 — Parse query

Classify query type:
- definition
- comparison
- argument
- exam-format answer
- study guide
- case application
- concept map
- gap analysis

Identify likely chapters, concepts, and expected output style.

## Step 2 — Retrieve from vault

Progressive retrieval — read in this order, stop when you can answer fully:

1. `knowledge/index.md` — identify relevant chapters and concepts
2. `knowledge/concepts/*.md` — matching concept pages (primary source)
3. `knowledge/chapters/*.md` — for argument structure and connections
4. `knowledge/test/**/*.md` — if exam-related query
5. `knowledge/resources/*.md` — ONLY when structured pages need source detail

Read actual Markdown files. Do not rely on memory or general knowledge.

**Max files to read:** Start with 3-5, expand only if answer is incomplete.

## Step 3 — Generate answer

Rules (see `references/writing-rules.md` for full spec):
- Ground EVERY substantive claim in the vault
- Cite chapter, concept, or test pages with wikilinks
- Use resource pages only when necessary for detail
- Do NOT present outside knowledge as course content
- If the vault lacks information, say so explicitly

Match register to query type:
- Definition → precise, structured
- Exam answer → formal, complete, exam-appropriate length
- Study guide → organized, progressive
- Comparison → parallel structure, clear contrasts

## Step 4 — Write practice file

See `references/vault-schema.md` for practice file schema.

```yaml
---
query: "<original question>"
date: <YYYY-MM-DD>
sources: ["[[chapters/chNN-xxx]]", "[[concepts/concept-a]]"]
---
```

Include sections:
- `## Answer` — the generated response
- `## Knowledge Base References` — claim-level citations
- `## Gaps & Uncertainties` — what the vault lacks
- `## Related Exam Questions` — if any exist in test/

## Gap Behavior

If the answer requires missing knowledge:
1. State the gap in `## Gaps & Uncertainties`
2. Add gap to `knowledge/index.md` under `## Coverage Gaps` if significant
3. Do NOT invent course-specific content to fill the gap

## Insert-at-cursor Mode

Only when user EXPLICITLY requests insertion into an existing note:
1. Read the target note first
2. Preserve all existing content
3. Insert only the generated section
4. Include citations
5. Do not overwrite user-authored text

Default remains: create new practice file.
