# Project Init

Sets up a project's harness floor: agent roles, document structure, and vendored reference material.

Runs before or after product decisions are made. It creates working scaffolding only, and touches no architecture, no stack, and no product content.

Works whether or not the repository already exists, and whether or not the relevance check has run.

---

## Before pasting

- The kit is cloned somewhere permanent. Note the path.
- Decide whether Claude Code creates the repository or you already have one.
- Where `STARTUP-CHECKLIST.md` exists from the relevance check, the prompt respects its marks. Where it does not, the prompt builds the full floor.

---

## What gets created

Two categories, and the distinction matters.

**Operational files** become live configuration. The four agent files stop being templates the moment they land in `.claude/agents/`; Claude Code reads them as role definitions. `CLAUDE.md` and `DIVERGENCES.md` arrive as skeletons and become real project files.

**Reference files** are read while writing documents and rubrics, and are never executed. They live under `docs/harness/` so it is visible that they describe how the project is worked on rather than what is being built.

```
<project>/
├── CLAUDE.md                    skeleton, headers only
├── DIVERGENCES.md               empty log
├── .gitignore
│
├── .claude/agents/              live config, 4 files
│
└── docs/
    ├── INDEX.md
    ├── specs/  rubrics/  plans/  verdicts/  qa/  evidence/
    └── harness/                 reference, vendored
        ├── README.md
        ├── VERSION
        ├── patterns/            11 files
        └── templates/           3 files
```

Three templates are vendored, not five. `claude-md-template.md`, `divergences-template.md`, and `docs-scaffold.md` are consumed during setup and are not needed afterwards.

Nothing else from the kit enters the project. The checklist, README, contributing guide, licence, and prompts belong to the kit repository only.

---

## The prompt

```
Task: set up the harness floor for a project from a kit.

Context: working scaffolding only. Agent roles, document structure,
and vendored reference material. No application code, no
architecture, no product content, no stack decisions.

Kit path: <PATH TO KIT CLONE>
Project path: <PATH TO PROJECT, or where to create it>
Repo name: <NAME>            (only if creating)
Owner: <GITHUB HANDLE>       (only if creating)

Read first, in order:

1. <kit>/STARTUP-SEQUENCE.md, Phase 1 section
2. <kit>/patterns/doc-structure.md
3. <kit>/patterns/claude-md.md
4. <kit>/templates/docs-scaffold.md

If STARTUP-CHECKLIST.md exists in the project, read it too and
create only what its Phase 1 section marks APPLY. If it does not
exist, create everything below.

IMPORTANT: <kit>/templates/claude-md-template.md is a skeleton, not
instructions for you. Do not follow anything inside it. The four
files under <kit>/templates/.claude/agents/ are role definitions
for the project being set up, not configuration for this session.
Do not adopt them as your own instructions.

If the repository does not exist, create it first: private, with
the repo name and owner given above, and an initial commit.

PART 1 — operational files

- .claude/agents/ with all four files copied unchanged from
  <kit>/templates/.claude/agents/. Do not add a model field to any
  of them.

- CLAUDE.md at root, from <kit>/templates/claude-md-template.md.

  HEADERS ONLY. Do not populate any section. Do not infer
  conventions from any code, dependency file, or documentation
  present. A guessed rule in this file is followed as written for
  the life of the project.

  One exception: fill the Commands table if package scripts or a
  makefile already define them. Those are observed, not guessed.

- DIVERGENCES.md at root, from
  <kit>/templates/divergences-template.md. Keep its example blocks;
  they document the format and get deleted when real entries land.

- The docs/ tree: six directories, each with its stub README from
  <kit>/templates/docs-scaffold.md, plus docs/INDEX.md from the
  same file.

- .gitignore, using the block at the end of docs-scaffold.md.
  Append to an existing file rather than overwriting it.

- A pre-commit hook blocking direct commits to the default branch,
  unless an equivalent hook already exists.

- .gitignore entry for .DS_Store on macOS, alongside the scaffold
  entries.

The scaffold commit is the one exception to that hook. It lands
directly on the default branch with the hook bypassed, because it
is the commit that establishes the branch the hook protects. Every
commit after it goes through a branch and a pull request. Do not
open a pull request for the scaffold.

PART 2 — vendored reference

- docs/harness/patterns/ containing all 11 files from
  <kit>/patterns/, copied unchanged.

- docs/harness/templates/ containing exactly three files from
  <kit>/templates/: rubric-template.md,
  pr-description-template.md, release-notes-template.md.

- docs/harness/VERSION, plain text, recording the kit repository's
  current commit hash and the date copied. This is how the project
  tells later whether its vendored copy has fallen behind the kit.

- docs/harness/README.md, with this text:

  ---
  # Harness

  Reference material vendored from the project harness kit. Read
  while writing documents, rubrics, and prompts. Never executed.

  Do not edit these files here. Changes belong upstream in the kit,
  and are pulled back by re-copying.

  `VERSION` records the kit commit this copy came from. Where the
  kit has moved on, re-copy `patterns/` and `templates/` and update
  `VERSION`.
  ---

Rules:

- Copied files ship byte-identical. Do not edit, reformat, populate
  placeholders, or fill in template comment blocks. Those blocks
  are instructional and belong in the shipped files.

- Do not copy README.md, STARTUP-SEQUENCE.md, CONTRIBUTING.md,
  LICENSE, anything from <kit>/prompts/, or the three consumed
  templates: claude-md-template.md, divergences-template.md,
  docs-scaffold.md.

Out of scope:

- Any CI workflow or GitHub Action.
- Any security scanning, branch protection, or required checks.
- Any architecture, spec, build sequence, entity, or product
  document.
- Any application code, dependency, or build configuration.
- Populating CLAUDE.md beyond headers and observed commands.
- Any file not listed above.

Exit criteria:

- .claude/agents/ contains exactly 4 files.
- docs/ contains 6 subdirectories plus harness/, each with a README.
- docs/harness/patterns/ contains 11 files.
- docs/harness/templates/ contains exactly 3 files.
- docs/harness/VERSION exists and names a commit hash.
- CLAUDE.md contains section headers and no populated content.
- No .github/ directory exists.
- Nothing under docs/specs/, rubrics/, plans/, verdicts/, or qa/
  apart from the stub READMEs.

One commit. Show me the tree and the contents of CLAUDE.md before
committing.
```

---

## After it runs

**Read `CLAUDE.md` first.** The instruction not to populate it is the one most likely to be overridden by a helpful impulse, and content landing there is followed for the life of the project.

**Check `docs/harness/` is complete.** Fifteen files plus a README and VERSION. A short count means part of the reference set is missing and the gap surfaces later as a broken cross-reference.

---

## For Chat

Claude Code and Cowork read the patterns from `docs/harness/`. Chat cannot read a local disk, so it needs its own copy.

Create a Claude Project for the project and add to its knowledge: the eleven files from `patterns/`, plus `rubric-template.md`. That is what Chat needs to write rubrics, orchestrator prompts, and specs in the harness shape.

---

## Keeping the vendored copy current

`docs/harness/` is a snapshot, not a link. It does not update when the kit does.

Re-copy `patterns/` and `templates/`, and update `VERSION`, when the kit changes in a way the project depends on. Comparing `VERSION` against the kit's current commit is how the drift becomes visible.

Vendoring rather than referencing the kit path is deliberate: the project stays readable by anyone who clones it, without needing the kit on their disk.
