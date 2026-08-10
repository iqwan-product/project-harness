# CI and Security Gate

Reference for what to set up at Gate A and Gate B, and the decisions each involves.

Neither gate opens during Phase 1. Continuous integration written before a stack is selected is configuration against a guess. A security review configured before there is anything sensitive to review costs money on every run and finds nothing.

This file covers shape and decision points. It prescribes no specific tools.

---

## Gate A — Continuous integration

**Trigger: the stack is selected and something runs.**

### Checks

Three, on every pull request:

| Check | Purpose |
|---|---|
| Type check or compile | Catches the largest class of mechanical error at the lowest cost |
| Test suite | Behavioural regression |
| Lint and format | Removes style from review |

Ordering matters when the runner is shared. Put the fastest check first so a broken change fails in seconds rather than after a full test run.

### Path filtering

Documentation-only changes skip the code checks.

This is not only a time saving. Once the security gate opens, its expensive check needs excluding from documentation pull requests, and that exclusion is easiest to build on a filter that already exists.

Filter on changed paths rather than on labels. A label applied after a pull request is created is not atomic with creation, so a workflow triggering on creation has already fired before the label lands. Path filters evaluate against the diff and have no such window.

### Branch protection

Configured in the repository settings, not in a file. Manual, and no part of this kit can carry it.

- Require pull requests before merge to the default branch.
- Require the checks above to pass.
- Require the branch to be current with the default branch before merge, where the project's merge volume makes stale-branch failures likely.

Local hooks blocking commits to the default branch are the Phase 1 equivalent. They protect the author's own machine. Branch protection protects the repository, and both are worth having: hooks fail faster, protection cannot be bypassed.

### Test splitting

Where the suite has natural groupings, split it into parallel jobs early.

The split is close to free when the suite is small and becomes a structural change once it is large, because job boundaries interact with fixtures, shared state, and container startup. The point at which the split becomes obviously necessary is past the point at which it is cheap.

Groupings that usually work: by module, by whether a test needs a database or container, by unit against integration.

---

## Gate B — Security review

**Trigger: first code handling auth, personal data, payments, or secrets.**

### Layer one, static scanning

Three scanners, all cheap, all running on every change:

| Scanner | Catches |
|---|---|
| Secret detection | Credentials committed to history |
| Dependency audit | Known-vulnerable packages |
| Static analysis | Common code-level defect patterns |

Run these on the same trigger as the Gate A checks. They are fast enough that separating them buys nothing.

Secret detection scans history, not only the diff. A credential committed and then removed in a later commit is still in the repository and still compromised.

### Layer two, model-driven review

A review that reads the diff and reasons about it, as a separate check.

This one carries real per-run cost, which changes how it is configured:

- **Path filter it.** Documentation pull requests do not need it.
- **Fire it once on a converged pull request**, not on every push. Where the workflow uses draft pull requests, the draft-to-ready transition is the natural trigger. Work stays in draft until it is converged, and the review fires when the author declares it ready.
- **Do not have the orchestrator mark a pull request ready.** Marking ready is what triggers the expensive check, so it stays a human decision at a review checkpoint.

### Secret storage

The API key for a model-driven review is stored as a repository secret, configured in the repository settings.

This step is manual and unavoidable. No file in any starter kit can carry a credential, and a workflow referencing a secret that does not exist fails in a way that reads as a workflow bug rather than a missing configuration value.

### Suppression

Some pull requests should skip the expensive review. Documentation-only changes are the main case.

Use path filters. The label-based approach has the race condition described under Gate A: the workflow fires on creation, before a label can be applied.

Where a code pull request genuinely needs the review skipped, that is a decision with a reason, and the reason belongs in the pull request description.

### Security sequence document

Where the surface warrants it, a document listing the security gates in order, each with what it closes and what it does not.

It differs from the build sequence in that its items are not features and do not ship visibly. Without a document, they get deferred indefinitely, because nothing surfaces their absence.

Interleave the gates into the build sequence at the points where they become relevant. A gate scheduled after the feature it protects ships late.

---

## Scope limit

Static scanning plus a model-driven review plus branch protection catches committed secrets, known-vulnerable dependencies, and common code-level defects.

It does not evaluate an authorisation model, a tenancy boundary, or a data-exposure surface. Those are design questions, and no scanner reads a design. They need review at the point the design is made, which is before any of these gates run.

Stating the limit matters because a green pipeline reads as a clearance.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Pipeline written before the stack is selected | Configuration against a guess, rewritten. |
| Label-based workflow suppression | Race condition; the workflow fires before the label lands. |
| Expensive review on every push | Cost scales with commits rather than with changes reviewed. |
| An orchestrating agent marking a pull request ready | Removes the human decision that gates the expensive check. |
| Secret detection scanning only the diff | Misses credentials committed and later removed. |
| Integration suite split deferred until it hurts | The split becomes a structural change. |
| Treating a green pipeline as a security clearance | Design-level exposure goes unexamined. |
| Security gates appended after feature work | Gates ship after what they protect. |

---

## Related

- `patterns/build-sequence.md` for interleaving security gates.
- `patterns/agent-pattern.md` for the draft-to-ready transition and the verdict sweep.
- `patterns/pr-and-release-writing.md` for the documentation-only pull request that carries the verdict.
