# Divergences

Shipped code that knowingly departs from spec, with the condition for closing each gap.

**Before adding an entry, apply the test:** can a removal trigger be stated? If not, this is not a divergence. It is a change to what the spec should say, and the spec should be updated instead. Format and reasoning in `patterns/divergence-log.md`.

**ID format:** `<step-id>-D<nn>`. Numbering restarts within each step. IDs are never reused.

**Timing:** logged in the same pull request that creates the divergence.

---

## Active

<!--
Open divergences only. Newest first.
Copy the block below. Delete the example once real entries exist.
-->

### EXAMPLE-D01 — Short title

**Spec reference:** `docs/specs/<file>.md` §<section>
**What the spec says:** Quoted or closely paraphrased. One or two sentences.
**What shipped:** The actual behaviour.
**Why:** The trade-off accepted, not the constraint alone. "Time" is not a reason.
**Removal trigger:** A condition that can be evaluated true or false. "When we have time" cannot be.

---

## Resolved

<!--
Moved here on closure, not deleted. Two fields added.
Newest first.
-->

### EXAMPLE-D00 — Short title

**Spec reference:**
**What the spec says:**
**What shipped:**
**Why:**
**Removal trigger:**
**Resolved:** <date>
**Commit:** <hash or pull request reference>
