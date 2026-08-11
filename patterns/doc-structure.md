# Documentation Structure

Reference for the `docs/` layout, what each directory holds, and which contents are tracked in version control.

The structure separates documents by lifetime and audience. Specifications persist and are read constantly. Verdicts and run artifacts are consumed once and then serve only as an audit trail. Mixing the two makes the persistent documents hard to find and the transient ones hard to clear.

Create all six directories empty during Phase 1, each with a stub README stating what belongs there. Creating them upfront removes the placement decision at the moment a file needs a home, which is when placement decisions are made badly.

---

## Layout

```
docs/
├── INDEX.md              Map of the documentation set
├── DIVERGENCES.md        Log of shipped deviations from spec
├── specs/                Canonical specifications
├── rubrics/              Per-step exit criteria
├── plans/                Implementation plans, Path A steps only
├── verdicts/             Grader output
├── qa/                   Test cases and run reports
├── evidence/             Run-state and working artifacts
└── harness/              Vendored reference material
```

`DIVERGENCES.md` sits at the documentation root rather than inside a directory because it is written across every step and read constantly. Some projects place it at repository root instead. Either works; what matters is that it is not nested where it can be forgotten.

---

## Directories

### `specs/`

Canonical specifications. The documents the conventions file names in its hierarchy.

Long-lived, edited rarely, read constantly. Typical contents: architecture, scope, build sequence, domain entity definitions, and whatever documents carry the project's real complexity.

Tracked. Every change goes through a pull request. A specification edited directly on the default branch has no review and no record of why it changed.

### `rubrics/`

One rubric per build step, defining exit criteria. Written before the step runs, read by the implementer, grader, and QA.

Tracked. The rubric is the contract the work was graded against, and removing it removes the ability to answer later why something shipped as it did.

Naming: `<step-id>-rubric.md`.

### `plans/`

Implementation plans, produced by an investigation pass, for steps that need one. Path B steps have no plan and produce no file here.

Tracked, though with lower long-term value than rubrics. The plan describes intent before the work; the pull request describes what happened.

Naming: `<step-id>-implementation-plan.md`.

### `verdicts/`

Grader output, one per grading round.

Tracked, but never in the implementation pull request. The grader writes the verdict untracked in the worktree. The orchestrator sweeps it into a separate documentation-only pull request after merge.

The separation matters because a diff containing its own evaluation is self-referential, and because documentation-only pull requests can be excluded from expensive review checks by path filter, which they cannot be if they carry code.

Naming: `<step-id>-grader-verdict.md`. Where a step has multiple rounds, suffix the round.

### `qa/`

Test cases and run reports, one subdirectory per step.

```
qa/
└── <step-id>/
    ├── test-cases.md
    ├── report-run1.md
    └── screenshots/
```

Test cases and reports are tracked. Screenshots are not; they are large, numerous, and add nothing to a diff. Add the screenshot path to `.gitignore` during Phase 1, before the first QA run produces a hundred files.

### `evidence/`

Working artifacts produced during a run. Primarily the orchestrator's run-state file.

Untracked. These exist to let a dead session resume and have no value once the step merges. Add the directory to `.gitignore`, keeping a tracked stub README so the directory exists in a fresh clone.

### `harness/`

Reference material copied from the harness kit: the pattern documents and the templates the project uses while writing rubrics, pull requests, and release notes. Read while authoring; never executed.

```
harness/
├── README.md
├── VERSION
├── patterns/
└── templates/
```

Tracked. Vendoring rather than referencing a path outside the repository keeps the project readable by anyone who clones it, without requiring the kit on their disk.

Not edited in place. Changes belong upstream in the kit and are pulled back by re-copying. `VERSION` records the kit commit the copy came from, which is what makes drift visible.

This directory differs from the other six in that its contents are authored elsewhere. The six hold work produced by this project; `harness/` holds material describing how that work is done.

---

## Root files

### `INDEX.md`

A map of the documentation set: each document, one line on what it covers, and its authority level.

Its purpose is orientation for a reader arriving without context, which includes a fresh Claude session. Without it, finding the right document requires reading several wrong ones.

Update it when a document is added or renamed. An index pointing at a moved file is worse than no index, because it is trusted.

### `DIVERGENCES.md`

The log of shipped code knowingly departing from spec. Format in `patterns/divergence-log.md`.

---

## Naming

- Lower case, hyphen-separated. `build-sequence.md`, not `Build_Sequence.md`.
- Step identifiers prefix the file, not suffix it, so directory listings sort by step.
- No dates in filenames. Version control holds the history.
- No version numbers in filenames. A file named `architecture-v2.md` guarantees a `v3` and leaves nobody able to say which is current.

---

## Tracking summary

| Path | Tracked | Reason |
|---|---|---|
| `specs/` | Yes | Canonical, reviewed |
| `rubrics/` | Yes | The contract work was graded against |
| `plans/` | Yes | Intent record |
| `verdicts/` | Yes, in a separate documentation-only pull request | Audit trail, kept out of the diff it evaluates |
| `qa/*/test-cases.md`, `report-*.md` | Yes | Regression basis |
| `qa/*/screenshots/` | No | Volume, no diff value |
| `evidence/` | No | Transient, consumed within one run |
| `harness/` | Yes | Reference material; vendored so a clone is self-contained |

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Creating directories at first need | Placement decided under time pressure, inconsistently. |
| Verdicts committed in the implementation pull request | Self-referential diff, and the pull request can no longer skip code-review checks. |
| Screenshots tracked | Repository size grows quickly, diffs become unreadable. |
| Version numbers or dates in filenames | Ambiguity about which file is current. |
| An index that is not maintained | Trusted and wrong, which is worse than absent. |
| Specs edited on the default branch | No review, no recorded reason for the change. |

---

## Related

- `patterns/divergence-log.md` for the log format.
- `patterns/agent-pattern.md` for the verdict sweep at Stage 8.
- `patterns/claude-md.md` for what moves out of the conventions file into `specs/`.
