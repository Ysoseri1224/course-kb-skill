---
name: ingest-exam
description: |
  Ingest exam/test papers into the question database. Use when:
  - "这是往年考题" / "帮我整理这份试卷" / "把考试题加进去"
  - "add exam paper" / "ingest this past exam"
  - User provides a file that is clearly an exam, quiz, test, or assessment
  - User mentions "考题" / "试卷" / "往年题" / "mock exam"
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
  - PowerShell
---

# /ingest-exam <file>

Ingest a past exam or assessment paper into the knowledge base test database.

## Trigger

User provides a file path to an exam, test, quiz, or assessment paper.

## Critical Rule

**NEVER fabricate answers.** If no answer key is present, the Answer section stays blank.

## Pipeline

```
exam file → extract text → identify questions → classify type
→ link topics → write question entries → update test/index.md
→ update concept exam notes
```

## Step 1 — Extract

1. Read `.kb-config.md`.
2. Extract or transcribe the exam file using configured tools.
3. Store original in `test/resources/<exam-slug>` (copy, do not modify).
4. Preserve original wording exactly.

## Step 2 — Identify and classify questions

For each question found:
1. Assign sequential number: Q<year>-<N>
2. Identify question type (see `references/exam-patterns.md`)
3. Identify topic/concept links
4. Determine if single-topic or cross-topic
5. Extract options if MCQ
6. Extract answer if answer key is present (NEVER guess)

## Step 3 — Write question entries

See `references/exam-patterns.md` for question schema and filing rules.

Filing:
- Single-topic: `test/<topic-slug>/questions.md`
- Cross-topic: `test/cross-topic/questions.md`

Create directories as needed. Append to existing question files if they exist.

## Step 4 — Update test index

Create or update `test/index.md` with:
- Total question count by year
- Question count by type
- Question count by topic
- Questions without answers (informational flag)

## Step 5 — Update concept pages

For each concept linked to exam questions:
- Add reference under `## Exam Notes` section
- Note question types that target this concept
- Note exam year and question number

## Step 6 — Verify

- [ ] All topic wikilinks resolve
- [ ] Original file preserved in `test/resources/`
- [ ] No answers fabricated
- [ ] test/index.md updated
- [ ] Concept pages updated with exam notes

Report: questions extracted, by type, topics covered, questions without answers.
