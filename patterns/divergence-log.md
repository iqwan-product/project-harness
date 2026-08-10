# Divergence Log

Reference for recording shipped code that knowingly departs from spec.

A divergence is a deliberate, documented gap between what a specification says and what was built, with a stated condition for closing it. It is not a bug, not an oversight, and not a spec change.

The log exists because undocumented deviations become permanent by default. Six weeks later nobody can distinguish a considered trade-off from a mistake, and the safest assumption becomes that the code is correct, which erases the spec.

Opened empty during Phase 1. A log created at the moment of first need typically receives one entry and is then abandoned.

---

## The test

Before logging, apply one question: can a removal trigger be stated?

If yes, it is a divergence. Log it.

If no, it is not a divergence. It is a decision to build something different from what the spec says, which means the spec is now wrong. Update the spec instead.

This test does most of the work. Divergences with no removal trigger accumulate into a second, contradictory specification held in a log nobody reads.

---

## ID format

`<step-id>-D<nn>`

Numbering restarts within each step. The first divergence in step S12 is `S12-D01`, the second is `S12-D02`, and the first in S13 is `S13-D01`.

Restarting per step means an ID identifies its origin without a lookup. It also means IDs are assigned locally during a build step, without the implementer needing to know the global count.

IDs are never reused, including after resolution.

---

## Entry format

```
### <ID> — <short title>

**Spec reference:** Document and section that this departs from.
**What the spec says:** Quoted or closely paraphrased.
**What shipped:** The actual behaviour.
**Why:** The reason the deviation was accepted.
**Removal trigger:** The condition that closes this.
```

**Spec reference** points at a section, not a document. A divergence citing only a filename is not checkable.

**Why** records the trade-off, not the constraint alone. "Time" is not a reason. "The upstream provider does not expose the field required by the spec, and the workaround is bounded to one module" is.

**Removal trigger** is a condition that can be evaluated as true or false. "When we have time" cannot. "When the provider ships the field" or "before the first deployment to a second region" can.

---

## Resolution

When a divergence closes, move the entry to a **Resolved** section at the bottom of the file, and add two fields:

```
**Resolved:** <date>
**Commit:** <hash or pull request reference>
```

Moving rather than deleting preserves the record that the gap existed. That record is what makes a later reviewer able to distinguish "this was considered" from "this was never noticed".

The active section at the top holds only open divergences. Where the file is long enough that this matters, the active section is the only part read regularly.

---

## When to log

**In the same pull request that creates the divergence.** A divergence logged afterwards is a divergence not logged, because the moment for it passes with the merge.

The implementer logs it. The grader verifies the entry exists where the diff departs from the rubric. This makes the log a graded criterion rather than a courtesy.

---

## What is not a divergence

| Situation | Correct action |
|---|---|
| The spec is wrong and will be changed | Update the spec |
| A planned scope reduction agreed before the step | Update the build sequence, not the log |
| A bug | Fix it, or file it |
| A deferral with no trigger | Update the spec; this is a permanent change |
| A pattern used inconsistently across the codebase | A cleanup task, not a divergence |

The recurring confusion is between a divergence and a spec change. The difference is intent to converge. A divergence is temporary by definition and carries the condition of its own removal. A change with no intent to converge is a new specification.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Logging without a removal trigger | Creates a shadow specification. |
| Logging after the merge | The entry is written from memory and is usually wrong about why. |
| Deleting resolved entries | Loses the record that the trade-off was considered. |
| Reusing IDs | Breaks references from verdicts, commits, and other documents. |
| Using the log for bugs | Dilutes it until it stops being read. |
| Global sequential numbering | Requires knowing the global count to assign an ID. |

---

## Related

- `patterns/build-sequence.md` for step IDs.
- `patterns/rubric-shape.md` for making divergence logging a graded criterion.
- `templates/divergences-template.md` for the file skeleton.
