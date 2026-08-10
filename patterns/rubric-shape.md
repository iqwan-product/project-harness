# Rubric Shape

Reference for writing the rubric that defines a build step's exit criteria.

The rubric is the contract. The implementer builds against it, the grader grades against it and nothing else, and QA derives its test plan from it. Where any other document conflicts with the rubric, the rubric wins and the conflict is flagged rather than silently resolved.

A step without a rubric can still be built. It cannot be evaluated, because there is nothing to evaluate against except the implementer's own account of what it did.

---

## When to write one

Write a rubric before any step that will run through the agent pattern. Write it in the planning session, not the build session, and by a different process than the one that will implement it. A rubric written by the implementer describes what the implementer intends to do, which is not a test.

Two planning paths, decided at the build-sequence level:

| Path | Shape | Sequence |
|---|---|---|
| A | New data structures, or tension across modules | Rubric, then an investigation pass producing an implementation plan, then owner review, then the orchestrator |
| B | Wiring or light steps against structures that already exist | Rubric, then the orchestrator directly. The implementer brief serves as the plan. |

Most steps in a mature codebase are Path B. Routing a Path B step through a full investigation pass produces a plan restating what the rubric already said.

---

## Required sections

1. **Scope.** One paragraph on what the step delivers.
2. **Out of scope.** What this step explicitly does not ship. Load-bearing. Without it, an implementer with spare capacity extends scope, and the grader has no basis to object.
3. **Locked decisions.** Decisions settled during planning, numbered, stated so they need no re-derivation.
4. **Criteria.** Grouped by category. Each individually checkable.
5. **Automatic-fail criteria.** Kept separate from ordinary criteria. Any one failing fails the step regardless of the rest.
6. **Exit demonstration.** The observable sequence proving the step works. QA's test plan derives from this directly.
7. **Evidence required.** What the implementer must produce for the grader to grade at all.

---

## Criterion phrasing

Every criterion must be checkable by a reader who did not write the code and cannot ask the author what was meant.

**State the creation method, not only the artifact.** A criterion naming an artifact without naming how it is produced will accept a hand-made substitute that satisfies the description and omits what the tool would have generated.

The recurring case is generated files. A criterion reading "a migration exists for the new table" is satisfied by a hand-written migration file. The generator would also have written metadata that the migration system reads to track state. The hand-written file omits it. Type checks pass, tests pass, the grader passes it, and the failure surfaces only when someone builds from a clean database.

Two corrections, and the strongest rubrics carry both:

- Phrase the criterion as "generated with `<tool>`" rather than "exists".
- Add a static integrity check to the deterministic gate that verifies generated artifacts and their metadata agree.

The general form of the defect: any criterion describing an artifact's end state, where the process that produces it also produces something else the criterion does not mention.

**Make criteria observable, not only structural.** A structural criterion asks whether something is present. An observable criterion asks whether something behaves.

| Structural, weak | Observable, gradeable |
|---|---|
| An auth guard is applied to the endpoint | An unauthenticated request to the endpoint returns 401 |
| Tenant scoping is implemented | A request scoped to tenant A returns no rows belonging to tenant B |
| Error handling is present | A malformed payload returns a 400 with no internal detail in the body |

Structural criteria pass on the presence of code that does not work. Every criterion that can be phrased as a request and an expected response should be.

Some criteria are genuinely structural, such as file placement or a configuration value. Keep those, and keep them in the minority.

**One assertion per criterion.** A criterion containing "and" produces a verdict that is half true, and graders resolve that inconsistently. Split it.

---

## Automatic-fail criteria

A small set, listed separately, covering the invariants that make the step unsafe to merge if broken. Typically: no credential in source, no bypass of the authorisation layer, no direct data access outside the designated abstraction, no commit to the protected branch.

They sit apart from ordinary criteria because the grader's instruction differs. Ordinary criteria are counted. An automatic-fail criterion failing fails the step immediately, and the remaining count is irrelevant.

Keep the list short. A long list of automatic-fail criteria means ordinary criteria have been promoted for emphasis, which removes the distinction's meaning.

---

## Exit demonstration

A numbered sequence a person performs against the running system, with the expected result at each step. It is the rubric section that survives longest, because it becomes the QA test plan and later the regression case.

Write it as an operator would perform it: start state, actions, observable results. Not as a list of things that should be true.

Where the step has no user-facing surface, the demonstration is a sequence of requests and expected responses.

---

## Evidence required

State what the implementer must produce for the grader to work. Without this section the grader reads a diff and infers intent, which is the weakest form of grading available.

Standard set:

- The inventory output from the implementer's first pass.
- A self-check listing every rubric criterion with a one-line claim and a file or line reference.
- A table mapping phases to commits.
- Exit-demonstration output.
- Any divergences logged, with removal triggers.

The self-check is a claim, not a verdict. State that in the grader's prompt so it verifies rather than accepts.

---

## Common defects

| Defect | Effect |
|---|---|
| Criteria phrased as intentions rather than observations | Verdicts full of inconclusive results. |
| Missing out-of-scope section | Scope creep with no basis for objection. |
| Artifact existence without creation method | Passing structural checks, failing at runtime. |
| Compound criteria joined with "and" | Verdicts that are partially true and graded inconsistently. |
| Exit demonstration written as assertions | QA cannot derive a test plan and invents one. |
| Automatic-fail list used for emphasis | The distinction stops carrying meaning. |
| Rubric written by whoever will implement it | Restates the plan instead of testing it. |

---

## Pre-ship checklist

- [ ] Out-of-scope section present and specific.
- [ ] Every criterion is one assertion.
- [ ] Every criterion that could be observable is phrased observably.
- [ ] Every generated artifact names its generator.
- [ ] Automatic-fail criteria listed separately and kept short.
- [ ] Exit demonstration is a numbered operator sequence with expected results.
- [ ] Evidence section names what the implementer must produce.
- [ ] Locked decisions numbered and stated without ambiguity.

---

## Related

- `patterns/agent-pattern.md` for how the rubric is consumed by grader and QA.
- `patterns/build-sequence.md` for the Path A and Path B routing decision.
- `templates/rubric-template.md` for the skeleton.
