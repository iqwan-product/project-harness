# Relevance Check

Phase 0 prompt. Paste into Claude Code in the new project's repository, or in an empty directory where the repository does not exist yet.

It produces a project-local copy of the startup checklist with every item marked. The output is a starting position, not a decision. Read what it dropped.

---

## Before pasting

Answer the four fit-check questions and paste the answers with the prompt.

1. How many surfaces does this project have?
2. Solo or team? Where a team, how many people touch the repository?
3. Does anything touch money, personal data, or authentication?
4. Will work span many sessions, or is this a short project?

Where the stack is already chosen, say what it is. Where it is not, say so.

---

## The prompt

```
You are helping set up a new project using a startup checklist. Your job is to
decide which items apply, not to execute any of them.

Read these files:

1. STARTUP-SEQUENCE.md — the checklist
2. patterns/*.md — the reference material each item points at

Here is the project:

<PASTE THE FOUR ANSWERS HERE>
<PASTE ANY OTHER CONTEXT: what it does, what exists already, deadline pressure>

Produce a file named STARTUP-CHECKLIST.md containing every item from
STARTUP-SEQUENCE.md, in the same order, each marked with exactly one of:

  APPLY   — do this, at the phase stated
  DEFER   — do this later, with a trigger condition you must state
  DROP    — do not do this, with a reason you must state

Rules for your decisions:

- Default to APPLY. The checklist exists because these items were expensive to
  learn. Drop only where you can state a specific reason this project does not
  have the condition the item addresses.

- DEFER requires a trigger condition that can be evaluated true or false. "When
  the project is bigger" is not a trigger. "When a second person has commit
  access" is.

- DROP requires a reason naming the absent condition, not a judgement about
  effort. "This project has one surface, so step ordering is not a coordination
  cost" is a reason. "This seems like overhead" is not.

- The four gates are already conditional. Do not drop a gate because its trigger
  has not fired. Mark it APPLY and state the trigger.

- Where an item's applicability depends on something you were not told, mark it
  APPLY and add a question at the end of the file rather than guessing.

Then add two sections at the end:

## Questions
Anything you need answered before the marks are reliable.

## Sequence
The APPLY items in execution order, grouped by phase, as a flat checklist.
This is what gets worked through.

Do not create any other files. Do not run any git operations. Do not start
executing checklist items.
```

---

## After it runs

Read the drops first. The failure mode of an automated relevance pass is agreeing with itself, and drops are where that shows.

Three checks worth making by hand:

**Did it drop the documentation layer on a project that will run long?** The layer's cost is visible immediately and its value is not, which biases against it.

**Did it defer a gate whose trigger has already fired?** Gate B in particular. A project handling authentication on day one has already met the trigger.

**Did it drop anything for effort rather than for an absent condition?** That is the reason the prompt forbids it, and it is the most common violation.

Answer the questions section, then re-run with the answers where the marks changed materially.
