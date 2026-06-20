# Pause Conditions

Shared rules for when to pause and ask the user vs. proceed automatically.

## PAUSE and ask the user when:

- Canonical resource already exists (ask before overwrite)
- Extraction candidates materially disagree (>20% content divergence)
- Tables with likely exam-relevant content are corrupted in all candidates
- OCR ambiguity changes meaning of a term or claim
- Source file appears damaged or unreadable
- A concept conflict changes course meaning (not just adds perspective)
- Existing resource replacement would be required
- The insertion target is ambiguous (user didn't specify where)
- Configured extraction tools are missing and no fallback exists

## Do NOT pause for:

- Routine index updates
- Creating new concept pages
- Creating new chapter pages from clear source material
- Creating new practice files
- Reporting normal coverage gaps
- Minor extraction differences (formatting, whitespace, heading levels)
- Adding new sources to existing concept/chapter pages
- File-by-file progress during batch operations
