# Documentation Scaffold

Contents for the `docs/` tree created during Phase 1. Each block below is one file. The project-init prompt splits them out; they can also be copied by hand.

Layout and reasoning in `patterns/doc-structure.md`.

---

## `docs/INDEX.md`

```markdown
# Documentation Index

Map of the documentation set. Update when a document is added, renamed, or moved.

An index pointing at a moved file is worse than no index, because it is trusted.

## Canonical specifications

| Document | Covers | Authority |
|---|---|---|
| `specs/architecture.md` | Stack, boundaries, deferred decisions | 3 |
| `specs/build-sequence.md` | Step order and dependencies | 4 |

## Per-step artifacts

| Directory | Contents |
|---|---|
| `rubrics/` | Exit criteria, one per step |
| `plans/` | Implementation plans, Path A steps only |
| `verdicts/` | Grader output |
| `qa/` | Test cases and run reports |
| `evidence/` | Run-state, untracked |
| `harness/` | Vendored reference material from the kit |

## Root files

| File | Contents |
|---|---|
| `DIVERGENCES.md` | Shipped deviations from spec, with removal triggers |
```

---

## `docs/specs/README.md`

```markdown
# Specifications

Canonical specifications. The documents the conventions file names in its hierarchy.

Long-lived, edited rarely, read constantly.

Tracked. Every change goes through a pull request. A specification edited directly on the default branch has no review and no recorded reason for the change.
```

---

## `docs/rubrics/README.md`

```markdown
# Rubrics

One rubric per build step, defining exit criteria.

Written before the step runs. Read by the implementer, the grader, and QA.

Naming: `<step-id>-rubric.md`.

Tracked. The rubric is the contract the work was graded against; removing it removes the ability to answer later why something shipped as it did.
```

---

## `docs/plans/README.md`

```markdown
# Implementation Plans

Plans produced by an investigation pass, for steps routed to Path A.

Path B steps have no plan and produce no file here.

Naming: `<step-id>-implementation-plan.md`.

Tracked. Lower long-term value than a rubric: the plan describes intent before the work, the pull request describes what happened.
```

---

## `docs/verdicts/README.md`

```markdown
# Verdicts

Grader output, one per grading round.

Naming: `<step-id>-grader-verdict.md`, with a round suffix where a step has more than one.

The grader writes the verdict untracked in the worktree. The orchestrator sweeps it into a separate documentation-only pull request after the implementation merges.

It never appears in the implementation pull request diff. A diff containing its own evaluation is self-referential, and a pull request carrying code cannot be excluded from code-review checks by path filter.
```

---

## `docs/qa/README.md`

```markdown
# QA

One subdirectory per step.

```
<step-id>/
├── test-cases.md
├── report-run1.md
└── screenshots/
```

Test cases and reports are tracked. Screenshots are not: large, numerous, and no diff value. The screenshot path is in `.gitignore`.
```

---

## `docs/evidence/README.md`

```markdown
# Evidence

Working artifacts produced during a run. Primarily the orchestrator's run-state file.

Untracked, apart from this README. These exist so a dead session can resume, and have no value once the step merges.
```

---

## `docs/harness/README.md`

```markdown
# Harness

Reference material vendored from the project harness kit. Read while writing documents, rubrics, and prompts. Never executed.

Do not edit these files here. Changes belong upstream in the kit, and are pulled back by re-copying.

`VERSION` records the kit commit this copy came from. Where the kit has moved on, re-copy `patterns/` and `templates/` and update `VERSION`.
```

---

## `.gitignore` additions

```
docs/evidence/*
!docs/evidence/README.md
docs/qa/*/screenshots/
```

Add these during Phase 1, before the first QA run produces a hundred files.
