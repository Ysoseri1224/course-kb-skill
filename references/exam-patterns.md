# Exam Patterns Reference

This document defines exam question recognition, schema, and filing rules for the ingest-exam skill.

## Question Types

| Type | Identifier |
|------|-----------|
| MCQ | Multiple choice with options A/B/C/D |
| Short Answer | Brief factual response expected |
| Essay | Extended argument or discussion |
| Calculation | Numerical computation required |
| Case Study | Apply concepts to a scenario |
| True/False | Binary choice |
| Fill-in-blank | Complete a statement |

## Question Schema

```markdown
## Q<year>-<number>

**Type:** MCQ | Short Answer | Essay | Calculation | Case Study | True/False | Fill-in-blank
**Topics:** [[concepts/concept-a]], [[chapters/chNN-xxx]]
**Source:** [[test/resources/<exam-filename>]]
**Year:** <YYYY>

### Question
<exact question text, preserved verbatim>

### Options
- A. ...
- B. ...
- C. ...
- D. ...

### Answer
<leave blank if no answer key provided — NEVER fabricate>

### Analysis
<leave blank if no analysis available>
```

## Critical Rule

**Do NOT fabricate answers.** If no answer key is present in the source, the Answer section stays blank. Do not use general knowledge to guess answers.

## Filing Rules

### Single-topic questions
```
knowledge/test/<topic-slug>/questions.md
knowledge/test/<topic-slug>/by-type/<type>.md
```

### Cross-topic questions
```
knowledge/test/cross-topic/questions.md
knowledge/test/cross-topic/by-type/<type>.md
```

## Topic Assignment

- Identify the primary concept(s) each question tests
- Link to concept pages via `**Topics:**` field
- If a question tests 2+ concepts from different chapters, file under `cross-topic/`
- If a question tests multiple concepts from the same chapter, file under that topic

## Exam Signal Propagation

After filing questions, update relevant concept pages:
- Add exam question references under `## Exam Notes`
- Note question types that target each concept
- Note frequency (how often this concept appears in exams)

## Index Update

After ingest-exam, update or create `test/index.md` with:
- Total question count by year
- Question count by type
- Question count by topic
- Questions without answers (informational)
