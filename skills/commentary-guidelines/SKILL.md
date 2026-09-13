---
name: commentary-guidelines
description: >
  Read this before writing or editing code comments or docstrings intended for end users. Guards against writing the decision process into the artifact instead of the current state, and against restating design-doc specs as comments.
---

# No Meta-Commentary in Comments

Comments are read later, by someone with no access to the conversation that produced them. They should describe how the code in front of the reader behaves right now — not the reasoning or discussion that led there.

## Rules

A comment earns its place only if the code would be meaningfully harder to understand without it. Default to not writing one. Most lines need no comment at all.

- **Don't duplicate the spec.** What a type is, what fields it carries, how it relates to other types — that lives in the design doc. The declaration below the comment already says it.
- **Don't restate obvious code.** A variable, condition, or data structure that reads clearly needs no gloss.
- **Comment non-obvious current behavior only.** A constraint that looks removable but isn't, a unit or ordering assumption, an invariant the code depends on silently.
- **Describe what the code does, not what it should do.** Requirements, intended architecture, and future design belong in documentation.
- **Don't explain *why* a decision was made** rather than what the current behavior is.
- **Don't flag anything as undecided, provisional, or temporary** ("for now", "not final yet"), and don't reference current circumstances expected to change.
- **Don't narrate rejected alternatives.**
- **Keep it short.** Write the shortest durable phrasing that carries the information. Volume is not thoroughness.
- `TODO`/`FIXME` markers are exempt — they mark a gap between the spec and the current code, not a description of behavior. Keep them to the unimplemented/buggy item itself, and don't attach the spec text or the discussion that produced them.

## Examples

Ask what a reader would get wrong without the comment. Usually nothing, and the comment goes. When something real is buried in it — a constraint, a mechanism — keep that and drop the rest.

Example:
- Instead of: `// only using 1 GPU right now but this might change, so not hardcoding it`
- Write: nothing.
- Why: provisional circumstances belong in developer docs, not in commentary.

Example:
- Instead of: `// Belt is not a single tier. It has independent speed and CarrierKind specifications.`
- Write: nothing.
- Why: duplicating the spec.

Example:
- Instead of: `// we found out hardcoding broke things so we changed it`
- Write: `// requires CLI flags — hardcoded values break under multi-GPU`
- Why: the constraint is non-obvious on code itself so commentary is needed; the discovery story is decision history.

Example:
- Instead of: no commentary
- Write: `// insertion order is load order — recipes resolve ingredient IDs against entries already inserted, so a sorted container breaks forward references`
- Why: an invariant a reasonable cleanup would silently break.

Example:
- Instead of: `// We accumulate into sum, but each addition of a small value to a large accumulator loses the low-order bits of the small value. So we track the lost amount in c, subtract it from the next input, and recompute c from the difference between the rounded result and what we intended to add.`
- Write: `// Kahan summation — c holds the low-order bits lost each round and feeds them back in`
- Why: the algorithm is non-obvious enough to need a comment, but naming it and stating the mechanism in one line is enough.

Example:
- Instead of: no commentary on `dirty: Vec<usize>` in a struct
- Write: `// indices into self.nodes, invalidated by any removal`
- Why: a field's usage contract is non-obvious, so the comment carries what the declaration can't.

## Scope

This applies to comments and docstrings. It doesn't apply to commit messages, PR descriptions, CLAUDE.md, specification docs, or other process-facing docs, where explaining a decision is normal and expected.
