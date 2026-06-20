# Writing Rules & Invariants

This document defines the invariants and writing rules that all course-kb skills must enforce.

## Invariants

Unless the local project rules explicitly override them:

1. `chapters/` contains argument structure only — no inline concept definitions.
2. `concepts/` is the single source of truth for definitions.
3. `resources/` contains canonical extraction artifacts and is read-only after creation.
4. `test/resources/` contains original exam files and is never modified.
5. `practice/` is append-only; create new files rather than editing old ones.
6. Every concept mentioned in a chapter must be wikilinked.
7. Every chapter mentioned in a concept must be wikilinked.
8. Do not silently overwrite existing knowledge.
9. Do not silently resolve contradictions between sources.
10. Do not fill course knowledge gaps with general model knowledge.
11. All substantive generated answers must cite structured vault pages.
12. Raw resources are evidence, not the primary learning interface.

## Chapter Writing Rules

A chapter page SHOULD answer:
- What argument is the lecturer building?
- Why does this topic matter in the course?
- Which concepts are central?
- What claims or emphases are exam-relevant?
- How does this chapter connect to other chapters?

A chapter page SHOULD NOT contain:
- Complete concept definitions (those belong in concepts/)
- All examples and cases (those belong in concepts/)
- Slide-by-slide notes (write logical argument structure instead)

Additional rules:
- Use wikilinks for all concepts
- Add `## Conflicts` section if a new source contradicts existing chapter claims
- Preserve existing material unless explicitly updating with attribution

## Concept Writing Rules

A concept page SHOULD answer:
- How does this course define the concept?
- What claims does the lecturer make about it?
- Which concepts is it related to or contrasted with?
- What examples illustrate it?
- How might it appear in exams?

Additional rules:
- Concept pages are the only place for definitions
- Examples and cases belong in concept pages
- Exam signals belong in concept pages
- If a concept already exists, append or update carefully — do not overwrite
- If sources conflict, note the conflict explicitly
- Update frontmatter sources and chapter links

## Practice Writing Rules

A practice document SHOULD:
- Answer the user's query
- Use a register appropriate to the task
- Cite knowledge-base references with wikilinks
- State gaps explicitly
- Link related exam questions when available

## Incremental Maintenance Rules

When adding a new source:
1. Do not rename existing concepts unnecessarily
2. Do not duplicate concepts under slightly different slugs
3. Append new source perspectives to existing concepts
4. Add conflicts when sources disagree
5. Update `sources:` frontmatter
6. Update `updated:` dates
7. Update index coverage
8. Run lint after ingest

## Conflict Handling

When sources disagree on a concept or claim:
- Note the conflict explicitly in the affected page
- Do NOT silently resolve by choosing one source
- Add a `## Conflicts` section with both source citations
- Let the user decide resolution

## General Writing Style

- Prefer precise course-local language
- Preserve source attribution
- Use wikilinks consistently
- Keep chapter and concept responsibilities separate
- Avoid generic textbook filler unless present in course materials
- Make conflicts and gaps visible
