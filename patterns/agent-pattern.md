# Agent Pattern

Reference for running a build step through an orchestrator and a set of subagents. Covers the roles, the stage machine, the conventions that keep a long run stable, and the caps that stop it looping.

The model is orchestrator-workers combined with evaluator-optimizer. One long-lived session acts as orchestrator. It spawns worker subagents, gates transitions between stages, and loops an evaluation cycle until the work satisfies a rubric. The orchestrator does not write code.

---

## When to use it

Use it when a build step meets all three conditions:

- A rubric exists that states the exit criteria.
- The step spans more than one file or surface, so a single-pass prompt would produce work no one has evaluated.
- The work ships. Exploratory or throwaway work does not justify the overhead.

Do not use it for single-file changes, documentation edits, or investigation passes. The pattern costs several agent spawns and two review stops. Below a certain step size that cost exceeds the value of the review.

One orchestrator drives one build step and produces one implementation pull request. Do not combine two steps into one orchestrator.

---

## Roles

| Role | Spawned | Tools | Responsibility |
|---|---|---|---|
| Orchestrator | The session itself | Full | Gates stages, runs the deterministic gate and QA, merges. Writes no code. |
| Implementer | Subagent | Full | Inventory pass, then builds against the rubric. Opens a draft pull request. |
| Grader | Subagent | Read-only | Walks every rubric criterion and writes a verdict. Applies no fixes. |
| Reviser | Subagent | Full | Fixes only the items the verdict flagged. Capped per spawn. |
| QA | Parent context, not spawned | Read-only plus browser automation | Drives the running application against the rubric's exit demonstration. |

Read-only status for the grader and QA is declared in the agent definition files rather than instructed in the prompt. This is deliberate. An agent with write access that is told not to write will sometimes write, usually to fix something small it noticed while evaluating. Removing the capability removes the failure.

QA runs in the parent context rather than as a spawned subagent because browser-automation tooling connected at session level does not reliably propagate into spawned subagents. Verify propagation in the current environment before changing this. When QA runs in the parent context, the orchestrator states the read-only boundary as a self-imposed rule, since the parent session has full tools.

---

## The stage machine

| Stage | Action | Stop condition |
|---|---|---|
| 1 | Spawn implementer. Inventory pass, build, open draft pull request. | Implementer returns a summary or escalates. |
| 2 | Review checkpoint, post-implementation. | Owner responds proceed, wait, or abort. |
| 3 | Run the deterministic gate. If green, spawn grader. | Gate result recorded, verdict written. |
| 4 | Branch on the verdict. | One of satisfied, needs revision, failed. |
| 5 | On needs-revision or a red gate: spawn reviser once, re-gate, re-grade. | Maximum one revision round per loop. |
| 6 | QA in parent context. | QA report written. |
| 7 | Review checkpoint, post-grader and QA. | Owner responds approve, fix, or abort. |
| 8 | Finalise: sweep the verdict file, mark ready, watch CI, merge, sync, clean up. | Pull request merged, worktree removed. |
| 9 | Escalate. | Escalation document written, run stops. |

Stages 2 and 7 are mandatory stops. They sit at the two points where a human decision changes the outcome: after code exists but before it is evaluated, and after evaluation but before merge. Checkpoints at every stage produce approval fatigue and get skipped.

Stage 9 is reachable from several stages and always terminates the run.

---

## Run conventions

Include all five near the top of every orchestrator prompt.

**Deterministic gate before the grader.** Cheap mechanical checks (type check, full test suite, static greps for load-bearing invariants) run in the worktree before the grader is spawned. Any failure routes straight to a revision round. A grading round spent discovering a compile error is a wasted round.

**Summaries, not transcripts.** Subagents return a short summary, ten lines or fewer, plus a pointer to a file or pull request. Bulk output such as the inventory pass or a diff goes into the pull request description or a worktree file. The orchestrator's context is the scarce resource in a long run; filling it with subagent output degrades every later stage.

**Run-state file.** The orchestrator maintains an untracked state file updated at every stage boundary, recording stage, pull request number, last verdict, revision rounds used, QA runs used, and next action. If the run dies or its context degrades, a fresh session resumes from the file instead of re-deriving position from git history.

**Verdict token on the first line.** The grader's return begins with a single machine-readable token stating the result. The orchestrator branches on that line, not on prose. Prose verdicts get misread when they contain hedging, which they frequently do.

**No model version pinned.** Agent files carry no model field. Subagents inherit the session's model. Named versions become stale and the failure is silent.

---

## Orchestrator prompt structure

Author in this order.

1. **Title and role preamble.** States that the session orchestrates and does not write code. Names the five roles.
2. **Run conventions.** The five above.
3. **Preconditions.** Verified before anything is spawned: prerequisite step merged, rubric readable, agent files present, working tree clean. Any failure writes an escalation document and stops. Initialise the run-state file here.
4. **Stage machine table.** The nine stages.
5. **Locked decisions.** Every decision settled during planning, numbered, stated plainly so subagents do not re-derive them. Include the rule that the rubric wins on conflict and that a subagent flags a conflict rather than choosing.
6. **Stage 1, implementer.** The largest section. Anatomy below.
7. **Stage 2, checkpoint.** A printed block: pull request URL, inventory summary, commit list, divergences. Then a structured question to the owner.
8. **Stage 3, gate and grader.** Gate commands, then the grader sub-prompt.
9. **Stage 4, verdict branch.** The three-way table.
10. **Stage 5, reviser.** Sub-prompt, cap, re-gate then re-grade.
11. **Stage 6, QA.** Parent-context reminder plus the numbered test plan derived from the rubric's exit demonstration.
12. **Stage 7, checkpoint.** Verdict, QA result, recommended action, structured question.
13. **Stage 8, finalise.** Verdict sweep, mark ready, CI watch, merge, sync, cleanup, report, delete run-state.
14. **Stage 9, escalate.** Escalation document, structured question, worktree cleanup.
15. **Carry-forward rules.** The standing invariants below.

---

## Implementer sub-prompt

Inside the Stage 1 block, in this order.

- **Task.** One paragraph covering what the step does and what is explicitly excluded.
- **Ordered reads.** Rubric first as the contract, then the implementation plan where one exists, then the agent definition, then the conventions file, then the specific spec sections and code files the step touches. Name eight to ten files with the order fixed. Unordered reads produce implementers that form an opinion from a spec section before reading the rubric that supersedes it.
- **Locked decisions echo.** Restate the numbered decisions so the implementer's own context carries them.
- **Branch and worktree bootstrap.** Create an isolated worktree and run the bootstrap script that reproduces the working environment: copy gitignored configuration from the primary checkout, install dependencies, prepare local data stores. The script is idempotent and belongs in the repo, not the prompt.
- **Inventory pass.** Validate the plan's assumptions against the live tree. Stop after the inventory, return a short summary, and wait for acknowledgement before writing code. The stop is load-bearing. It catches plans written against a tree that has since moved.
- **Work order.** Follow the plan's phases. One commit per phase. Each phase leaves the tree green. Give each phase its file list and a commit message.
- **Out of scope.** Restate what the rubric excludes.
- **Rules.** Invariants in imperative form, particularly those a static check will later grep for.
- **Exit criteria.** The rubric's exit demonstration as checks, plus type check and tests green, plus a draft pull request open.
- **Evidence for the grader.** Placed in the pull request description: the inventory output, a rubric self-check with one line per criterion, a phase-to-commit table, exit-demonstration evidence, and any logged divergences.
- **Stop and return.** Draft pull request only. Do not mark ready. Do not merge. Return a short summary with commit hashes, the pull request URL, and divergences.

---

## Grader and reviser sub-prompts

**Grader.** Ordered reads: rubric, then the diff, then the pull request description, then referenced files. Grading rules to state explicitly: walk every criterion and skip none; any automatic-fail criterion triggers a fail regardless of the rest; a pass requires cited evidence, and where evidence is unavailable the correct result is inconclusive rather than a pass; the pull request description is a claim, not a verdict. Include the behavioural verification commands: exercise the endpoints or interfaces the step opened, grep for the invariants the step was supposed to preserve. The verdict is written to a file in the verdicts directory, untracked in the worktree. The return begins with the result token, then counts, then the top three issues.

**Reviser.** Reads the verdict, which defines its scope, plus the rubric for context. Scope is strict: fix only the flagged items, with no adjacent fixes, no refactoring, no renaming, no optimisation. Per-spawn cap of three files and one hundred lines, enforced in the agent definition, with an escalation to the owner if exceeded. One commit per fix. Push and return the hashes with a note per fix. The reviser does not re-trigger the grader; the orchestrator does.

---

## Caps

| Cap | Value |
|---|---|
| Reviser rounds per grading loop | 1 |
| Reviser rounds across the step lifecycle | 2 |
| Reviser scope per spawn | 3 files, 100 lines |
| QA runs total | 2 |
| Grader re-spawns | After each reviser round. A second needs-revision in one loop exceeds the cap. |

Exceeding any cap escalates to the owner at a checkpoint. Never retry silently past a cap. The cap exists because a step that needs three revision rounds usually has a rubric problem or a scope problem, and further rounds do not surface either.

---

## Standing invariants

Encode these in every orchestrator.

- No commits to the default branch without a pull request.
- The verdict file never appears in the implementation pull request diff. It is written untracked in the worktree and swept into a separate documentation-only pull request at Stage 8. Mixing an evaluation artifact into the diff it evaluates makes the diff self-referential and pollutes review.
- The pull request stays in draft until converged. The owner marks it ready. Marking ready is what triggers the expensive review checks, so the orchestrator does not do it.
- Genuine build-time deviations from spec are logged with a removal trigger. A planned spec change is not a divergence.
- Worktree cleanup is the orchestrator's responsibility, at Stage 8 and Stage 9 both. Orphaned worktrees accumulate silently.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Grading before the deterministic gate | A grading round spent on a compile or test failure. |
| Returning full inventory output or diffs to the orchestrator | Context exhaustion in later stages of a long run. |
| Pinning a model version | Stale reference, silent failure. |
| Committing the verdict file in the implementation pull request | Self-referential diff. Add a grep guard at Stage 8. |
| Marking ready or merging before the Stage 7 checkpoint | Removes the only review gate before merge. |
| Letting a subagent re-derive settled scope | Contradicts earlier decisions. Restate them; the rubric wins on conflict. |
| Silent retry past a cap | Masks a rubric or scope defect. |

---

## Pre-ship checklist

Before handing an orchestrator prompt to a run:

- [ ] Run conventions block present, all five.
- [ ] Preconditions verify the prerequisite step, rubric, agent files, and a clean tree.
- [ ] Locked decisions cover every decision settled in planning.
- [ ] Implementer reads are ordered and name the exact files the step touches.
- [ ] Work order maps to plan phases, one commit each, with commit messages.
- [ ] Deterministic gate commands are unambiguous and contain no behavioural checks.
- [ ] Grader writes the verdict untracked, Stage 8 sweeps it, the grep guard is present.
- [ ] QA runs in parent context and its test plan mirrors the rubric's exit demonstration.
- [ ] Caps and escalation paths are explicit at every stage.
- [ ] No model version named anywhere.

---

## Related

- `patterns/rubric-shape.md` for what the grader grades against.
- `patterns/build-sequence.md` for whether a step needs a plan before an orchestrator.
- `templates/.claude/agents/` for the four role definitions.
