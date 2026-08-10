# Rubric — <Step ID>: <Title>

<!--
TEMPLATE. Copy to docs/rubrics/<step-id>-rubric.md.
Written in the planning session, before the build session, and not by whatever
will implement it.

This is the contract. The implementer builds against it, the grader grades
against it and nothing else, and QA derives its test plan from it.

Guidance in patterns/rubric-shape.md. Delete every comment block once written.
-->

**Step:** <ID>
**Planning path:** A | B
**Depends on:** <Step IDs>

---

## Scope

<!-- One paragraph. What this step delivers, in behavioural terms. -->

## Out of scope

<!--
What this step explicitly does not ship. Load-bearing.

Without it, an implementer with spare capacity extends scope and the grader
has no basis to object. Where something is deferred to a later step, name
the step.
-->

-

## Locked decisions

<!--
Settled during planning. Numbered so subagents can echo them without
re-deriving. State the decision, not the debate.
-->

**LD1.**
**LD2.**

---

## Automatic-fail criteria

<!--
Invariants that make the step unsafe to merge if broken. Any one failing
fails the step regardless of the rest.

Keep short. A long list means ordinary criteria were promoted for emphasis,
which removes the distinction's meaning.
-->

| ID | Criterion |
|---|---|
| AF1 |  |

---

## Criteria

<!--
Grouped by category. Each individually checkable by someone who did not write
the code and cannot ask what was meant.

Three rules:

1. ONE ASSERTION EACH. A criterion containing "and" produces a verdict that is
   half true and gets graded inconsistently. Split it.

2. OBSERVABLE, NOT STRUCTURAL, wherever possible.
   Weak:  "An auth guard is applied to the endpoint"
   Good:  "An unauthenticated request to the endpoint returns 401"
   Structural criteria pass on the presence of code that does not work.

3. NAME THE CREATION METHOD for generated artifacts.
   Weak:  "A migration exists for the new table"
   Good:  "A migration generated with <tool>, with matching journal metadata"
   A criterion describing an end state accepts a hand-made substitute that
   omits whatever the generator would also have produced.
-->

### <Category>

| ID | Criterion |
|---|---|
| C1 |  |
| C2 |  |

---

## Exit demonstration

<!--
A numbered sequence performed against the running system, with the expected
result at each step. Written as an operator would perform it: start state,
actions, observable results.

Not a list of things that should be true. QA derives its test plan from this
directly, and where it is written as assertions QA invents a plan instead.
-->

**Preconditions:**

1. **Action.** Expected:
2. **Action.** Expected:

---

## Evidence required

<!--
What the implementer must produce for the grader to grade at all.
Without this the grader reads a diff and infers intent, which is the weakest
grading available.
-->

- Inventory output from the implementer's first pass.
- Self-check: every criterion above, one line each, with a file or line reference.
- Phase-to-commit table.
- Exit-demonstration output.
- Divergences logged, with removal triggers.
