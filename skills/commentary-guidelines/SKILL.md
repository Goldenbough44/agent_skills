---
name: commentary-guidelines
description: >
  Read this before writing or editing code comments or docstrings. Comments exist to help whoever edits the code next, not to preserve the discussion or restate the spec.
---

# What Comments Are For

A comment is not a log. It describes the code as it now stands, for a colleague of twenty years' experience seeing this file for the first time.

They don't need the code narrated back to them. What they can't get from the code is what it's for, what it assumes, when to reach for it, and what breaks if they change it. A reason that still holds is worth a line; how the code got that way — the path, the attempts, the discussion — is not, and it crowds out the answers that matter.

Be concise.

## Worth writing

A field or parameter read from elsewhere carries a usage contract — units, coordinate frame, what an index refers to, valid range, ownership, when the value is updated. If any of that isn't in the name or the type, state it at the declaration. Callers can't infer it, and there is nowhere else for them to look.

Beyond the above, comments are worth writing where someone could break something with a reasonable change, or where a line saves the reader from working out a mechanism. Elsewhere the code speaks for itself.

State when to reach for a function and when not to, wherever the name doesn't settle it. Nothing at the call site reveals it.

## Not worth writing

Where a comment wouldn't get used by someone changing the code, leave it out.

For example, these don't help anyone.

- **Decision history.** Why an approach was chosen, what was rejected, what "we found out", what is provisional — "for now", "not final yet", "this may change". Keep a real constraint if there is one; drop the story around it.
- **Spec text.** Implementing from a spec doesn't make that text a comment. It has a home (e.g. specification docs) already, and it goes stale independently of the code.

## Scope

This applies to comments and docstrings. It doesn't apply to commit messages, PR descriptions, CLAUDE.md, specification docs, or other process-facing docs, where explaining a decision is normal and expected.

In an existing codebase, take the cue from the comments already there — what they cover, and how much they say.
