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
| `templates/` and `prompts/` | Files to copy, and prompts to paste into Claude Code or Claude chat project files. |

Start with `STARTUP-SEQUENCE.md`. The patterns are read when the checklist points at them, not upfront.

---

## What it is not

**Not a stack.** No language, framework, database, or cloud provider. Those choices belong to the project, and a kit that made them would only fit projects that had made the same ones.

**Not a project template.** Nothing here scaffolds an application. It scaffolds the documentation, review, and agent layer around one.

---

## Getting started

### 1. Clone the kit, once

Somewhere permanent. Prompts read from this path, and reference material is copied from it into each project. Cloning again per project is not needed; `git pull` when the kit is updated.

```
git clone https://github.com/<owner>/project-harness.git ~/project-harness
```

Then read `STARTUP-SEQUENCE.md` to see what the kit sets up and what it deliberately leaves out.

### 2. Set up a project repo

Copy the prompt from `prompts/project-init.md`, fill in the kit path and project path, and paste it into Claude Code.

It creates the repository if one does not exist, then builds two things. Operational files: four agent role definitions in `.claude/agents/`, a `CLAUDE.md` skeleton with headers only, an empty divergence log, and the six-directory `docs/` tree. Vendored reference: the pattern documents and three templates copied into `docs/harness/`, with a `VERSION` file recording which kit commit they came from.

It writes no product content. No architecture, no stack, no specs. Those are decisions, and the prompt does not make them.

### 3. Give Chat the same reference material

Claude Code reads the patterns from disk. Chat cannot, so it needs its own copy.

Create a Claude Project for the work, then add these twelve files to its project knowledge, from the kit clone:

- all eleven files in `patterns/`
- `templates/rubric-template.md`

Chat reads project knowledge automatically. No prompt is needed to activate it.

This supplies reference material, not working conventions. Output rules, decision logging, and the division of work between Chat, Cowork, and Claude Code go in the project's custom instructions, which is a separate field and a separate decision.

### 4. Decide what actually applies, when you have the context

Run `prompts/relevance-check.md` against the project. It produces `STARTUP-CHECKLIST.md`, marking every checklist item apply, defer with a trigger, or drop with a reason.

Its main value is gate timing. Gate B opens on the first code touching auth, payments, or personal data, which is day one on some projects and never on others.

Review the drops by hand. An automated relevance pass agrees with itself, and it will drop the documentation layer on a project that turns out to run for months, because that cost is visible immediately and the value is not.

Steps 2 and 4 run in either order. The floor is cheap and near-universal, so it is commonly built before scope is settled.

### Then

Core documents, then a trial run of the agent pattern on one small step. Those are work, not prompts. `STARTUP-SEQUENCE.md` covers both.

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

Three sources qualify: an incident where something failed and the correction generalises, a capability change where the tooling moved and an item is now wrong or newly possible, or a practice change where a different approach proved better across real work.

Every item states what it prevents or enables, not only what to do. An item without its reason cannot be assessed for applicability, so the next reader drops it, correctly.

Full threshold in `CONTRIBUTING.md`.

---

## Licence

MIT. Use it, fork it, strip out what does not fit.
