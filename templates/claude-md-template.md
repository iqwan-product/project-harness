# CLAUDE.md

<!--
TEMPLATE. Copy to repository root as CLAUDE.md.

Create it with headers only during Phase 1. Populate after Phase 2, once the
core documents exist.

Target under 200 lines. Where it grows past that, move content out rather than
trimming wording. Guardrails in patterns/claude-md.md.

Delete every comment block, including this one, once populated.
-->

## Project

<!-- Two or three lines. What this is and what surfaces it has. Not a pitch. -->

## Commands

<!--
Operations run often enough to be worth not looking up.
Build, test, lint, typecheck, migrate, seed, dev server.
One line each. Omit anything run rarely.
-->

| Purpose | Command |
|---|---|
|  |  |

## Document hierarchy

<!--
Conflict order, most authoritative first, ending with the code.
Code last is the point: where code disagrees with a spec, the code is wrong.
-->

1.
2.
3. `CLAUDE.md` files, this and any directory-scoped ones
4. Code

<!--
Where the project marks unresolved decisions, state the rule for meeting one:
stop and flag, do not guess.
-->

## Directory map

<!--
Where things live. Enough to prevent a search, not a full tree.
-->

```
```

## Universal locked decisions

<!--
Cross-cutting invariants any change might violate.
State the decision. Point at the fuller reasoning in the architecture doc.
Do not restate the reasoning here.
-->

- **<Decision name>** — <what it is>. See `<doc reference>`.

## Prohibited patterns

<!--
Highest-value section. Two columns, both required.

The reason column is not decoration: without it, an obsolete rule stays
forever because no reader can tell whether it still applies.

Where a hook or static check already enforces something mechanically, note
that rather than restating the rule.
-->

| Prohibited | Why |
|---|---|
|  |  |

## Behavioural principles

<!--
How to approach work here. Short list. Four or five at most.
Examples of the kind of thing that belongs: read the governing spec before
coding; flag conflicts rather than choosing; stay inside stated scope.
-->

1.

## Skills

<!--
One line per skill: name, and when it applies.
The pointer belongs here. The content does not.
Authoring pattern in patterns/skill-pattern.md.
-->

| Skill | Applies to |
|---|---|
|  |  |

## Directory-scoped conventions

<!--
Where a subdirectory carries its own CLAUDE.md, list it here with one line
on what it covers.

Directory files inherit this file and do not restate it.
-->
