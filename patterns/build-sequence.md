# Build Sequence

Reference for the document that orders the work: what gets built, in what order, and what each step depends on.

It differs from a roadmap in two ways. It records what each step proves rather than only what it delivers, and it records how each step will be planned. Both fields exist to remove decisions from the moment the step starts, when they get made badly.

Written during Phase 2, amended continuously, read at the start of every build step.

---

## Step format

Each step is a numbered heading with a fixed field set.

```
### <Step ID> — <Title>

**Status:** Not started | In progress | Done | Blocked
**Where:** Chat | Claude Code | Manual
**Depends on:** <Step IDs, or None>
**Planning path:** A | B
**Proves:** <What is true after this step that was not before>
**Delivers:** <What ships>
**Out of scope:** <What this step explicitly does not do>
```

**Proves** is the field a roadmap omits. It states the capability the step establishes, which is what determines whether a later step can proceed. A step that delivers three endpoints and proves nothing new is usually two steps merged or one step that should be deferred.

**Out of scope** at the sequence level prevents the same scope argument recurring when the rubric is written. Where a step deliberately leaves something for a later step, name the later step.

---

## Status markers

Four states. Keep them literal and greppable.

| Marker | Meaning |
|---|---|
| Not started | No branch, no rubric |
| In progress | Rubric exists or work has begun |
| Done | Merged to the default branch |
| Blocked | Cannot proceed; the blocking condition is named inline |

Done means merged, not implemented. A step that is built and unmerged is in progress. The distinction matters because dependency checks in orchestrator preconditions verify merge state, not intent.

Blocked always names what unblocks it. A blocked marker without a condition becomes permanent.

---

## Planning path

Two routes from a step to an orchestrator prompt. The choice is recorded in the sequence so it is not relitigated.

| Path | Applies when | Sequence |
|---|---|---|
| A | New data structures, or tension across modules that must be resolved before code | Rubric, investigation pass producing an implementation plan, owner review, orchestrator |
| B | Wiring, or work against structures that already exist | Rubric, orchestrator directly |

Path B is the majority in a codebase past its early steps. Routing a Path B step through an investigation pass produces a plan that restates the rubric, costs a full session, and adds a document that will be read once.

Route to Path A when any of these hold:

- The step creates or alters persistent data structures.
- Two modules disagree about ownership of the same concept.
- The implementation approach is genuinely undecided, not merely undocumented.

Where none hold, Path B.

---

## Ordering

Three principles.

**Dependencies before dependents, stated explicitly.** The depends-on field is checked mechanically in orchestrator preconditions, so it must name step IDs and not descriptions.

**Security gates interleaved, not appended.** Where the project has a separate security sequence, its gates sit inside the build sequence at the points where they become relevant, not in a block at the end. A gate scheduled after the feature it protects is a gate that ships late. Number them in their own series so they remain distinguishable.

**Prove capability before scaling it.** The first step exercising a new pattern is deliberately small. This is the same reasoning as the trial run in Phase 3 and applies again whenever the project adopts a new pattern mid-build.

---

## Amendment

The sequence changes. Steps get split, merged, deferred, and dropped.

When a step is split, the original ID is retired and the parts take suffixed IDs. Reusing the original ID for one of the parts breaks every reference to it.

When a step is deferred, it stays in the document with its status and a note on what deferred it. Deleting it loses the record that it was ever planned, and the question returns later as a new idea.

When a step is dropped, mark it dropped with a reason. Same reasoning.

---

## Relationship to other documents

| Document | Relationship |
|---|---|
| Architecture doc | Deferred decisions with forcing conditions map to steps. A forcing condition firing usually creates a step. |
| Rubrics | One per step. The rubric expands the step's proves and out-of-scope fields into criteria. |
| Divergence log | Divergences reference the step ID that produced them. |
| Conventions file | Does not restate the sequence. Points at it. |

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Steps without a proves field | No basis for deciding whether a later step can start. |
| Done marked at implementation rather than merge | Precondition checks pass against unmerged work. |
| Blocked without a named unblocking condition | Permanent blocked status. |
| Security gates appended at the end | Gates ship after what they protect. |
| Deleting deferred or dropped steps | The question returns as a new idea. |
| Reusing a step ID after a split | Breaks every existing reference. |
| Defaulting every step to Path A | A full planning session spent producing a restated rubric. |

---

## Related

- `patterns/rubric-shape.md` for expanding a step into criteria.
- `patterns/agent-pattern.md` for what the orchestrator verifies in its preconditions.
- `patterns/architecture-doc.md` for deferred decisions that generate steps.
