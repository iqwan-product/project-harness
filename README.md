# Project Harness

A setup checklist and reference set for running a software project with Claude.

It covers what to set up, in what order, and which items to defer until a project has earned them. It does not cover what to build or what to build it with.

---

## What this is

Three things:

| Part | Contents |
|---|---|
| `STARTUP-SEQUENCE.md` | The checklist. Three phases of setup, four gates that open later, and a set of standing rules. |
| `patterns/` | Reference material. What goes in each document, how the agent workflow runs, when a gate opens. |
| `templates/` and `prompts/` | Files to copy, and prompts to paste into Claude Code. |

Start with `STARTUP-SEQUENCE.md`. The patterns are read when the checklist points at them, not upfront.

---

## What it is not

**Not a stack.** No language, framework, database, or cloud provider. Those choices belong to the project, and a kit that made them would only fit projects that had made the same ones.

**Not a project template.** Nothing here scaffolds an application. It scaffolds the documentation, review, and agent layer around one.

**Not a standard.** It came out of one codebase. Where something is undertested, the relevant file says so.

---

## Getting started

1. Read `STARTUP-SEQUENCE.md`.
2. Run `prompts/relevance-check.md` in Claude Code against your project. It produces a project-local checklist with every item marked apply, defer, or drop.
3. Review the drops by hand. Automated relevance passes agree with themselves.
4. Run `prompts/project-init.md` to create the Phase 1 floor.
5. Work through Phase 2 and Phase 3.

The gates open later, on their triggers.

---

## Assumptions

**Three tools in three lanes.** Chat plans and writes prompts, Cowork performs surgical document edits, Claude Code owns git and code. Projects using only Claude Code can follow most of this; the lane rules in `patterns/three-tool-lanes.md` will not apply.

**Multi-session work.** The documentation layer exists because context does not survive session boundaries. Short projects do not need it.

**The agent pattern has a floor.** An orchestrator running implementer, grader, reviser, and QA roles suits build steps spanning a database and multiple surfaces. It is overhead on smaller work, and Phase 3 exists to test that before wider adoption.

---

## Contents

```
STARTUP-SEQUENCE.md        The checklist

patterns/
  agent-pattern.md         Orchestrator stages, roles, conventions, caps
  rubric-shape.md          Criterion phrasing and what a rubric must contain
  claude-md.md             Conventions file: length budget and placement rules
  doc-structure.md         The docs/ tree and what is tracked
  architecture-doc.md      Section outline and deferred-decision discipline
  build-sequence.md        Step format, status markers, planning paths
  divergence-log.md        Recording deliberate deviations from spec
  three-tool-lanes.md      Tool boundaries and document-edit routing
  skill-pattern.md         When a skill is the right container
  pr-and-release-writing.md  Writing for readers rather than for yourself
  ci-and-security-gate.md  What Gate A and Gate B involve

templates/
  .claude/agents/          Four role definitions
  claude-md-template.md
  divergences-template.md
  rubric-template.md
  pr-description-template.md
  release-notes-template.md
  docs-scaffold.md         The docs/ tree stubs

prompts/
  relevance-check.md       Phase 0: mark items apply, defer, or drop
  project-init.md          Phase 1: create the floor
```

---

## Extending it

The kit records what has been run, not what sounds sensible.

Three sources qualify: an incident where something failed and the correction generalises, a capability change where the tooling moved and an item is now wrong or newly possible, or a practice change where a different approach proved better across real work. Anything not yet run does not qualify. Corrections specific to one stack belong in that project's own documentation.

Every item states what it prevents or enables, not only what to do. An item without its reason cannot be assessed for applicability, so the next reader drops it, correctly.

Full threshold in `CONTRIBUTING.md`.

---

## Licence

MIT. Use it, fork it, strip out what does not fit.
