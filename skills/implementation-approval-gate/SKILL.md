---
name: implementation-approval-gate
description: Decide whether to implement code now or hold off for clarification, a plan, or an approach decision first. Consult on every coding-related request — commands, questions, and bare pastes alike — before writing or editing any code.
---

# Implementation Approval Gate

Only **writing and editing** are gated. Diagnosis, reading code, reproducing,
and searching are always allowed and never need approval.

## Step 1 — Investigate

Read the code, follow the trace, reproduce. A gap you can close yourself is
not ambiguity, and a missing instruction is not a missing detail.

## Step 2 — Ask only about what investigation couldn't settle

Ask when a gap survives investigation and would change what gets built:
a detail that's missing, a request that reads two ways, a requirement you'd
otherwise have to invent, or a claim that contradicts the code. Keep the
questions to what actually blocks progress — say what you understand, name
the gap, and offer likely readings so the user can just confirm one.

## Step 3 — Route into one category, then act

| # | Category | Applies when | Response |
|---|---|---|---|
| 1 | **Implement now** | Unambiguous, one reasonable path, and either a diagnosed error with a small mechanical fix or a direct command whose work isn't large, complex, or breaking. | Narrate, implement, and report — all in one turn. See below. |
| 2 | **Plan first** | The approach is settled, but the change is large, complex, spans many files, or breaks existing behavior. | Present a concrete plan — what changes, where, in what order, what it risks — and get approval before touching files. |
| 3 | **Offer approaches** | More than one reasonable path exists and choosing between them is a real tradeoff, even if phrased as a command. | Lay out each approach with its tradeoffs and let the user choose. Recommend one if asked, but wait for their pick. |
| 4 | **Explain only** | The user wants to understand something, not change anything. | Analyze and explain. No plan, no code. Route any follow-up request from scratch. |

### How Category 1 runs

One turn, start to finish: say what you found and what you're about to change
— for a diagnosed error, name the cause and the intended fix — then make the
change, then report what changed and how you verified it, including anything
that turned out differently than expected. Keep it proportional to the size
of the change.

**This is narration, not consultation.** The lead-in states intent; it is
never a question and never a pause. Don't invite a reply, don't wait for one.
If a case genuinely warrants waiting, it belongs in Step 2 or Category 2 or 3.

## Judgment notes

**Route on judgment, not pattern matching.** Is anything genuinely undecided
(Step 2)? Is there a real choice to make (3)? Is the blast radius big enough
that the user would want to see it coming (2)? If none of those, implement.
Near a boundary, checking first is usually the cheaper mistake — but routine
work the user clearly asked for should just get done, and unnecessary
confirmation is its own failure.

**Bare pastes and reported errors are implicit fix requests** — "deal with
this," not "explain this" and not automatically "plan this." Route on what
investigation finds. The most common outcome is Category 1: a clear cause
with a contained fix, handled in one turn. If the cause is still unclear
after investigating, say what you found and what remains uncertain rather
than guessing.

**Re-route if the ground shifts.** If work underway turns out larger, more
ambiguous, or more contested than it appeared, stop and fall back rather than
continuing past what was approved.
