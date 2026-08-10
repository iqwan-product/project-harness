# Project Init

Phase 1 prompt. Paste into Claude Code in the new project's repository, after the relevance check has produced `STARTUP-CHECKLIST.md`.

It creates the repository floor: documentation scaffold, agent definitions, skeleton conventions file, divergence log, and ignore rules. It writes no project content, because none has been decided yet.

---

## Before pasting

- The repository exists and has at least one commit.
- `STARTUP-CHECKLIST.md` exists from the relevance check.
- This kit is available to Claude Code, either cloned alongside or copied in.

---

## The prompt

```
Set up the Phase 1 floor for this project using a startup kit.

Read first:

1. STARTUP-CHECKLIST.md — the project-local checklist. Only create what its
   Phase 1 section marks APPLY.
2. <path-to-kit>/patterns/doc-structure.md
3. <path-to-kit>/patterns/claude-md.md
4. <path-to-kit>/templates/docs-scaffold.md

Then create, in this order:

1. The docs/ tree. Six directories, each with the stub README from
   templates/docs-scaffold.md. Plus docs/INDEX.md from the same file.

2. .gitignore additions, per the block at the end of templates/docs-scaffold.md.
   Append; do not overwrite an existing .gitignore.

3. DIVERGENCES.md at repository root, from
   templates/divergences-template.md. Keep the example blocks; they document
   the format and get deleted when real entries land.

4. .claude/agents/ with all four agent files from
   templates/.claude/agents/. Copy them unchanged. Do not add a model field.

5. CLAUDE.md at repository root, from templates/claude-md-template.md.

   HEADERS ONLY. Do not populate any section. Do not infer conventions from
   the code that exists. Conventions are decided in Phase 2, and a guessed
   rule in this file is still followed as written.

   The only exception: fill the Commands table if package scripts or a
   makefile already define them, since those are observed rather than guessed.

6. A pre-commit hook blocking direct commits to the default branch, if the
   checklist marks it APPLY and no equivalent hook exists.

Then report:

- What you created, as a tree.
- Anything the checklist marked APPLY that you did not create, and why.
- Anything already present that you left alone.

Do not commit. Do not open a pull request. Do not populate CLAUDE.md beyond
headers and observed commands. Do not create any file not listed above.
```

---

## After it runs

Review `CLAUDE.md` before anything else. The instruction not to populate it is the one most likely to be overridden by a helpful impulse, and content that lands there gets followed for the life of the project.

Then commit the scaffold as one commit. It is setup, not a feature, and does not need a pull request in a repository with no other contributors yet.
