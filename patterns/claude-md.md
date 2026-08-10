# CLAUDE.md

Reference for what belongs in the conventions file, what does not, and why length is the governing constraint.

`CLAUDE.md` loads into context on every session in the repository. That makes it the highest-leverage document in the project and the most expensive one to get wrong. Content in it is paid for on every request, whether or not the current task needs it.

Treat it as a context budget, not a wiki.

---

## Length

Target under 200 lines.

The number is not exact, but the failure mode past it is consistent. A file long enough to contain everything is long enough that individual rules stop being reliably followed, because they compete with each other for attention within a large block of text. Rules added to fix a specific problem stop working once the file crosses the threshold, and the response is usually to add more rules.

When the file grows past target, the correct action is almost never to trim wording. It is to identify content that belongs somewhere else and move it.

---

## What belongs

| Content | Why here |
|---|---|
| Prohibited patterns | Needed on every task. A rule that is not loaded is not enforced. |
| Universal locked decisions | Cross-cutting invariants that any change might violate. |
| Document hierarchy and conflict order | Determines which document wins. Required before reading any other document. |
| Directory map | Where things live. Cheap and prevents search. |
| Commands for common operations | Build, test, lint, migrate. Frequently needed, short. |
| Behavioural principles | How to approach work in this repo. Short list. |
| Pointers to specs and skills | One line each. The pointer belongs here, the content does not. |

---

## What does not belong

| Content | Where it goes instead |
|---|---|
| Full specifications | `docs/specs/`, referenced by one line |
| Domain rules used only on some tasks | A skill, loaded on demand |
| Per-step detail | The step's rubric |
| API contracts | A spec document |
| Historical rationale for a decision | The architecture doc's decision log |
| Anything true only in one directory | A directory-scoped conventions file |
| Onboarding narrative | `README.md` |

The test for any candidate line: does a task that has nothing to do with this need to know it? If no, it belongs in a skill or a spec.

---

## Prohibited patterns

The highest-value section, and worth writing as a table with two columns: the prohibited pattern and the reason it is prohibited.

The reason column is not decoration. Without it, a rule that no longer applies stays in the file permanently, because no reader can determine whether it is still live. With it, rules become removable.

Split mechanical rules from judgement rules. Mechanical rules, such as a forbidden literal string, are better enforced by a hook or a static check than by a line in a file, and the hook does not consume context. Keep only the rules that need judgement, and note which ones a hook already covers.

---

## Universal locked decisions

Cross-cutting decisions that any change might violate. Typical examples: identity separation between two user types, the boundary between an audit log and an activity log, which abstraction layers are mandatory and must not be bypassed, whether a value is data or code.

Each entry states the decision and points to the fuller reasoning elsewhere. The decision belongs here because it is enforced constantly. The reasoning belongs in the architecture doc because it is read rarely.

---

## Document hierarchy

State the conflict order explicitly, as a numbered list from most to least authoritative, ending with the code itself.

Code appearing last is the point of the list. Where code disagrees with a spec, the default assumption is that the code is wrong. Without a stated order the assumption inverts, since the code is what is directly observable.

Where the project uses status markers for unresolved decisions, state the rule for encountering one: stop and flag, do not guess. An unresolved decision resolved silently by an implementer becomes an architectural commitment nobody made.

---

## Directory-scoped files

In a repository with multiple applications or packages, a directory can carry its own conventions file. It loads only when work happens in that directory, so content specific to one surface costs nothing on unrelated tasks.

Split on this basis:

- Root file: rules true everywhere.
- Directory file: rules true only in that directory.

Directory files inherit the root and state that they do. They do not restate root content. A directory file duplicating root rules doubles the cost of changing them and guarantees they drift apart.

---

## Skills

Where the project has repeated task patterns with substantial guidance attached, that guidance belongs in a skill rather than the conventions file. Skills load on demand and cost nothing when idle.

The conventions file carries one line naming each skill and when it applies. Pattern for authoring one in `patterns/skill-pattern.md`.

---

## Maintenance

The file drifts. Decisions get superseded, prohibitions become obsolete, and pointers break when documents move.

Schedule a review rather than waiting for a symptom. Once every several build steps, read the file end to end against the current codebase and check three things:

- Does every prohibited pattern still describe something real?
- Does every pointer resolve?
- Has anything grown past a few lines that should now be a skill or a spec?

Drift is not detected during normal work, because a stale rule produces no error. It produces slightly wrong behaviour that reads as reasonable.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Appending a rule after every incident | Growth without review. The file crosses the threshold and all rules weaken. |
| Restating spec content instead of pointing at it | Two sources of truth that diverge. |
| Prohibitions without reasons | Rules that cannot be removed because nobody can tell whether they still apply. |
| Directory files repeating root content | Duplicate maintenance, guaranteed drift. |
| Mechanical rules stated in prose when a hook would enforce them | Context spent on something a check does for free. |
| Filling it during Phase 1 before conventions exist | Guessed rules, followed as written. |

---

## Related

- `patterns/doc-structure.md` for where the content that does not belong here goes.
- `patterns/skill-pattern.md` for moving task-specific guidance out.
- `templates/claude-md-template.md` for the section skeleton.
