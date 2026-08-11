# Startup Sequence

Setup checklist for a new project run with Claude. Three phases of setup, four gates that open later, and a set of standing rules.

The checklist is stack-agnostic. It specifies document shapes, agent roles, and gate triggers. It does not specify a language, framework, database, or cloud provider.

Not every item applies to every project. Phase 0 includes a step for marking items apply, defer, or drop.

---

## Assumptions

**Three tools, three lanes.** Chat plans and writes prompts. Cowork performs surgical multi-file document edits. Claude Code performs all git and code operations. Projects using only Claude Code can follow most of this; the lane rules in `patterns/three-tool-lanes.md` will not apply.

**The agent pattern has a floor.** An orchestrator running implementer, grader, reviser, and QA roles suits build steps spanning a database and multiple surfaces. It is overhead on smaller work. Phase 3 validates the pattern on one small step before wider adoption.

**Multi-session projects only.** The documentation layer exists because context does not survive session boundaries. Projects completed in a few sessions do not need it.

---

## Phase 0 — Fit check

**Where: Chat**

Four questions determine how much of the kit applies.

| Question | Effect on setup |
|---|---|
| How many surfaces? | One surface rarely needs a formal build sequence. Three or more makes step ordering the primary coordination cost. |
| Solo or team? | Solo makes the review checkpoints in the agent pattern self-review. On a team they are handoffs and need named owners. |
| Does anything touch money, personal data, or auth? | If yes, Gate B is mandatory and arrives early. If no, it may never open. |
| Will work span many sessions? | Determines whether the documentation layer earns its cost. |

Then run the relevance check.

- [ ] Paste `prompts/relevance-check.md` into Claude Code along with the four answers. It reads this checklist against the project and returns each item marked apply, defer with a trigger condition, or drop with a reason.
- [ ] Review the drops. The output is a project-local copy of this checklist, not a substitute for judgement.

The common failure with a checklist of this shape is adopting all of it. Roughly half the items are load-bearing only under conditions a given project may not have.

---

## Phase 1 — Repo setup

**Where: Claude Code, with one step at the user level**

Each item is cheap to do now and expensive to retrofit. `prompts/project-init.md` executes this phase, including repository creation where the repository does not yet exist.
This phase does not depend on Phase 0. The floor is near-universal, so it is often built before the project's scope is settled, and the relevance check follows.

- [ ] **Create the repo and block direct commits to the default branch.** A pre-commit hook is sufficient at this stage. Remote branch protection comes at Gate A, once CI checks exist to require.
- [ ] **Create the `docs/` scaffold.** Six directories for the project's own documents, empty, each with a stub README stating what belongs there. The seventh, `harness/`, is populated by the next item rather than created empty. Layout in `patterns/doc-structure.md`, stub contents in `templates/docs-scaffold.md`. Creating them upfront removes the placement decision at the moment a file needs a home, which is when placement decisions are made badly.
- [ ] **Create the `CLAUDE.md` skeleton, section headers only.** Do not populate it yet. Conventions are not known on day one, and guessed rules are still followed. Guardrails in `patterns/claude-md.md`, skeleton in `templates/claude-md-template.md`. The operative constraint: `CLAUDE.md` is a context budget, not a wiki. Target under 200 lines. Content exceeding that belongs in a spec or a skill.
- [ ] **Copy agent files into `.claude/agents/`.** Four roles, in `templates/.claude/agents/`. The grader and QA files declare read-only tool sets. This is a structural constraint rather than an instruction: an agent without write access cannot silently repair what it was asked to evaluate. Do not relax it.
- [ ] **Set the permission model at user level.** Deny paths for credentials and destructive commands, configured once in user-level Claude settings rather than per project. User-level settings reach every project and every worktree. Note the two denial types behave differently: a blocked file read is a hard stop, a blocked shell command is a prompt that can be approved past.
- [ ] **Open `DIVERGENCES.md` empty at repository root.** Format in `patterns/divergence-log.md`, skeleton in `templates/divergences-template.md`. Opening it before it is needed is deliberate. A log created at the moment of first need typically receives one entry and is then abandoned.
- [ ] **Copy the reference material into `docs/harness/`.** The eleven pattern documents plus the rubric, pull request, and release note templates, with a `VERSION` file recording the kit commit they came from. Vendoring rather than referencing a path outside the repository keeps the project readable by anyone who clones it. The copy is a snapshot and does not update when the kit does; comparing `VERSION` against the kit is what makes drift visible.

The scaffold costs roughly an hour and produces nothing visible. On short projects that cost is not recovered.

---

## Phase 2 — Core documents

**Where: Chat to decide, Cowork or Claude Code to write**

Three documents that serve as sources of truth. Claude reads them instead of re-deriving decisions each session.

- [ ] **Architecture doc.** Stack choices with reasoning, integration boundaries, and a numbered list of deferred decisions. Section outline in `patterns/architecture-doc.md`. The deferred-decisions list is the section most often omitted. Each entry records a decision consciously not made, plus the condition that will force it. Without it, deferrals become indistinguishable from oversights.
- [ ] **Build sequence.** Ordered steps with dependencies and status markers. Format in `patterns/build-sequence.md`. Two fields a standard roadmap omits: what each step proves, and whether the step requires an investigation pass before implementation or proceeds directly to execution. The second field prevents planning overhead on wiring work, which is typically a large share of steps.
- [ ] **Domain spec, where the domain warrants one.** Entity definitions, business rules, and whatever carries the project's real complexity. Named per project. Omit if the domain is thin.
- [ ] **Conflict order.** Record which document wins when two disagree, in `CLAUDE.md`. Without a stated order, Claude selects one, usually reasonably and not always as intended.

Populate `CLAUDE.md` now, after the core documents exist. It references them rather than restating them.

---

## Phase 3 — Trial run

**Where: Claude Code**

Run the agent pattern end to end on the smallest step that ships real work.

- [ ] **Write one rubric.** Shape in `patterns/rubric-shape.md`, skeleton in `templates/rubric-template.md`. Two requirements: state how each artifact is created and not only what must exist, and phrase every criterion so it can be observed rather than only structurally verified.

  A rubric naming an artifact without naming its creation method will accept a hand-written substitute. Migrations are the common case: a hand-written migration satisfies "a migration exists" while omitting metadata the generator would have produced, and the gap surfaces only on a fresh-database run. Structural pass does not imply behavioural pass.

- [ ] **Write one orchestrator prompt.** Pattern in `patterns/agent-pattern.md`.
- [ ] **Run it and record where it stalls.**
- [ ] **Correct the pattern rather than the step.** Failures at this stage recur on every subsequent step.

Two failure modes are typical on a first run. A rubric too vague for the grader to cite evidence against produces a hedged verdict. An orchestrator that omits already-settled decisions causes the implementer to re-derive them. Both are prompt defects and both are cheaper to find on a small step.

---

## Gates

Four setup items that are premature on day one and unsafe to skip permanently. Each has a trigger condition. Full detail for Gates A and B in `patterns/ci-and-security-gate.md`.

### Gate A — CI

**Trigger: the stack is selected and something runs.**

Earlier than this, pipeline config is written against a provisional stack.

- [ ] Type check, test, and lint on every pull request.
- [ ] Path filter excluding documentation-only changes. Filter on paths rather than labels: a label applied after a pull request is created is not atomic with creation, so a workflow triggering on creation has already fired.
- [ ] Remote branch protection requiring these checks.

Tool selection is project-specific.

Split integration tests into parallel groups early if the suite has natural groupings. The split is near-free at the start and becomes a structural change once the suite is large.

### Gate B — Security review

**Trigger: first code handling auth, personal data, payments, or secrets.**

- [ ] Static scanning in CI: secret detection, dependency audit, static analysis. Low cost, runs on every change. Secret detection scans history, not only the diff.
- [ ] A model-driven review as a separate check. This carries real per-run cost, so path-filter it and configure it to fire once on a converged pull request rather than on every push.
- [ ] API key stored as a repository secret. This step is manual, in the repository settings, and cannot be carried by any file in the kit.
- [ ] Security sequence document where the surface warrants one: ordered gates, each with what it closes. Interleave the gates into the build sequence rather than appending them.

Scope limit: static scanning plus branch protection catches committed secrets, known-vulnerable dependencies, and common code-level defects. It does not evaluate an authorisation model. That requires design review, which this gate does not provide.

### Gate C — Writing for readers

**Trigger: pull requests are read by someone other than the author.**

Public repositories, clients, contractors, and future maintainers all qualify.

- [ ] Adopt `templates/pr-description-template.md`.
- [ ] Relocate evidence out of the description. Rubric self-checks, inventories, and diffs belong in a comment or a linked file.
- [ ] Adopt `templates/release-notes-template.md` where releases are tagged.

Agent-driven workflows commonly route bulk output into the pull request body to keep the orchestrating session's context small. That remains correct. What changes at this gate is placement: a reader opening a pull request should find a description written for a person, with the audit trail linked rather than inlined. Split covered in `patterns/pr-and-release-writing.md`.

### Gate D — First remote deploy

**Trigger: code runs outside the development machine.**

- [ ] Secret management selected and wired. Environment files are no longer sufficient once code runs on infrastructure the developer does not control.
- [ ] Deployment documented to a standard that supports repeating it after a long gap.
- [ ] Rollback path tested.

Provider selection is an architecture decision and is out of scope for this kit.

---

## Standing rules

Rules that hold for the life of the project.

**Lane discipline.** Chat decides and writes prompts. Cowork edits documents. Claude Code touches git and code. Chat does not edit files. Claude Code does not decide what to edit. Detail in `patterns/three-tool-lanes.md`.

**Route document edits by shape.** One to three edits in a single file: inline before-and-after blocks. Four or more edits, or edits spanning files with an invariant that must stay consistent: Cowork. New file or majority rewrite: write the file directly and overwrite. Wrapping an already-drafted file in a Cowork prompt duplicates the content for no gain.

**Verify Cowork output on reasoning-heavy documents.** A confirmation that an edit applied confirms the find-and-replace executed, not that the replacement landed verbatim. Behavioural directives survive; explanatory prose compresses.

**Log divergences on the branch that causes them.** Where shipped code knowingly departs from spec, the log entry ships in the same pull request. Where no removal trigger can be stated, it is not a divergence and the spec should change instead.

**Check hooks before any prompt containing git operations.** A prompt instructing a commit to the default branch against a hook blocking commits to that branch produces a failed run and a wasted round trip.

**Do not pin model versions in prompts.** Version references become stale. Let the session's model carry.

---

## Extending this document

Additions record what has been run, not what sounds sensible.

Three sources qualify: an incident where something failed and the correction generalises beyond the project it occurred in, a capability change where the tooling moved and an item is now wrong or newly possible, or a practice change where a different approach proved better across real work. Anything not yet run does not qualify. Corrections specific to one stack belong in that project's own documentation.

Every item states what it prevents or enables, not only what to do. An item without its reason is dropped by the next reader, correctly, because applicability cannot be assessed.

Removals follow the same discipline. Where an item no longer earns its place, cut it and record why in the commit. Capability changes produce as many removals as additions.

---

## Out of scope

**Stack scaffolding.** No project template, dependency set, or build configuration.

**Cloud and deployment.** Gate D establishes that deployment is set up. Where and how is an architecture decision with cost, latency, and compliance implications.

**Domain skills.** The authoring pattern is in `patterns/skill-pattern.md`. Skills themselves are project-specific.

**Team process.** No ceremony, estimation, or branching strategy beyond pull-request-only. The pattern was developed for solo work and the assumptions are untested against a team.

**Testing strategy.** Gate A establishes that tests run in CI. Test scope and depth are project decisions.
