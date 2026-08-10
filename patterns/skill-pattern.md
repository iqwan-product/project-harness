# Skill Pattern

Reference for when to author a skill, and what goes in one.

A skill is guidance loaded on demand rather than on every request. It costs nothing when idle and full price when triggered, which inverts the economics of the conventions file. That inversion decides what belongs where.

---

## When a skill is the right container

Three conditions, all required:

- The guidance applies to a recurring task pattern rather than to all work.
- It is substantial enough that inlining it into the conventions file would push that file past its budget.
- The task pattern is recognisable from the request, so the skill can be triggered reliably.

Where any condition fails, choose differently.

| Situation | Container |
|---|---|
| Applies to every task | Conventions file |
| Applies to some tasks, substantial | Skill |
| Applies to some tasks, one or two lines | Conventions file, under a heading |
| Reference material read by humans, not applied by an agent | Spec document |
| Applies to exactly one build step | The step's rubric |

The third row is worth care. A skill holding two lines costs more in triggering ambiguity than it saves in context.

---

## File shape

```
skills/
└── <skill-name>/
    ├── SKILL.md
    └── references/          Optional
        └── <topic>.md
```

`SKILL.md` carries frontmatter with two fields and then the body.

```
---
name: <skill-name>
description: >
  When to use this skill, written as trigger conditions.
---

# <Skill Name>

<Body>
```

Reference files exist for content the skill needs sometimes rather than always. The body names them and states when to read them. This keeps the always-loaded portion small while leaving depth available.

---

## Writing the description

The description is the only part read before the skill triggers. It determines whether the skill fires at the right time, and it is where most skills fail.

Write it as trigger conditions, not as a summary of contents. A description saying what the skill contains requires the reader to infer applicability. A description saying when to use it does not.

Three things to include:

**The task patterns that should trigger it.** Concrete, in the vocabulary a request would actually use. Where several phrasings are plausible, list them.

**The cases that should not trigger it.** Adjacent task patterns the skill does not cover. Without this, skills with overlapping domains fire on each other's work.

**Any hard trigger.** Where the skill must fire on a specific artifact type or keyword, say so explicitly.

The test: given only the description and a request, could a reader with no knowledge of the skill's contents decide correctly whether to load it.

---

## Body

Directive rather than descriptive. A skill is read at the moment of doing the work, by something that will act on it.

Structure that works:

1. **Scope.** One paragraph on what the skill governs.
2. **Rules.** The constraints, stated imperatively.
3. **Procedure**, where the task has a fixed sequence. Numbered.
4. **Anti-patterns.** What goes wrong, and the consequence.
5. **Reference pointers.** Which reference file covers what, and when to read it.

Keep reasoning brief. The skill exists to produce correct behaviour, and extended justification competes with the instruction for attention. Where the reasoning matters enough to preserve, put it in a reference file.

---

## Skills against hooks

Some rules are better enforced mechanically than through guidance.

| Rule type | Enforcement |
|---|---|
| A forbidden literal string | Hook or static check |
| A required file naming convention | Hook or static check |
| A judgement about when a pattern applies | Skill |
| A multi-step procedure with decision points | Skill |

Mechanical enforcement is deterministic and costs no context. Where a rule can be expressed as a check, express it as a check and let the skill cover only the judgement around it.

---

## Maintenance

Skills go stale less visibly than the conventions file, because a skill that has stopped applying simply stops being useful when it fires, and that reads as the skill being unhelpful rather than wrong.

Review at the same cadence as the conventions file. Two questions:

- Has this skill fired at all recently, and did it help?
- Does the description still describe the tasks the project actually has?

A skill that has not fired in a long time is either badly described or no longer needed. Both cases warrant action.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Description written as a summary of contents | Fires unreliably. |
| No negative triggers | Fires on adjacent work it does not cover. |
| Descriptive rather than directive body | Read as context, not acted on. |
| Extended reasoning inline | Competes with the instruction. |
| A skill holding one or two rules | Triggering ambiguity exceeds the context saved. |
| Mechanical rules stated as guidance | A check would enforce them for free. |
| Project-specific skills shipped in a shared harness | Noise in every repository that adopts it. |

---

## Related

- `patterns/claude-md.md` for what moves out of the conventions file into a skill.
- `patterns/doc-structure.md` for what belongs in a spec instead.
