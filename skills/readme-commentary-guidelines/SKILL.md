---
name: readme-commentary-guidelines
description: >
  Read this before writing or editing code comments, docstrings, or README/documentation files intended for end users (not AI-facing planning docs like CLAUDE.md). Guards against writing the decision process into the artifact instead of the current state. Especially relevant right after a discussion about design choices, since that discussion tends to leak into the comment/README as narrative.
---

# No Meta-Commentary in Comments/README

Comments and README files are read later, by someone with no access to the conversation that produced them. They should describe current state and usage — not the reasoning or discussion that led there.

## The pattern to avoid

Right after discussing a design choice, there's a pull to preserve that context by writing it into the code. But a comment isn't a transcript or a design log. Watch for lines that:

- Explain *why* a decision was made rather than *what* the current behavior is
- Flag something as undecided, provisional, or temporary ("for now", "not final yet")
- Reference specific current circumstances expected to change (a specific machine, a specific team situation)
- Narrate alternatives that were considered and rejected

## The fix

State the result plainly. If the underlying point still matters for a future reader, keep it but strip it down to a generic, durable phrase — don't reference the fact that a decision or discussion happened.

Example:
- Instead of: `# dataset folder layout isn't finalized yet, so keeping it outside preprocessor/ for now`
- Write: `# dataset path is set via config`

Example:
- Instead of: `# only using 1 GPU right now but this might change, so not hardcoding it`
- Write: `# GPU settings are passed as CLI flags`

If the reason is a real technical constraint future maintainers shouldn't undo, keep the constraint but drop the story: `# requires CLI flags — hardcoded values break under multi-GPU` rather than `# we found out hardcoding broke things so we changed it`.

## Scope

This applies to comments, docstrings, and README-type docs. It doesn't apply to commit messages, PR descriptions, CLAUDE.md, or other process-facing docs, where explaining a decision is normal and expected.
