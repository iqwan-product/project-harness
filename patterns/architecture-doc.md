# Architecture Document

Reference for the document that records stack choices, system boundaries, and the decisions deliberately left open.

It answers one question repeatedly: why is it built this way. That question arrives from a new contributor, from a future maintainer, and from every fresh Claude session that reads the codebase and forms a reasonable opinion contradicting a decision made months earlier for reasons no longer visible.

Written once during Phase 2, amended when a decision changes, read whenever a change touches a boundary.

---

## Section outline

Numbered sections, because other documents will cite them by number. Section numbers that stay stable are more valuable than section titles that read well.

1. **Purpose and scope.** What the system is. One page at most.
2. **Stack.** Each significant technology choice, with the reason and the alternatives rejected. The rejected alternatives matter more than the choice; without them a reader assumes the choice was a default rather than a decision.
3. **System shape.** Surfaces, services, and what talks to what. A diagram helps and does not replace the text.
4. **Data model boundaries.** Which entities belong to which domain, and which relationships are deliberately absent. Absent relationships need stating explicitly. A missing foreign key looks like an oversight unless the document says it is intentional.
5. **Identity and access model.** Who authenticates, against what, with which session scope. Where multiple user types exist, state whether their identities are shared or separate, and whether any session crosses the boundary.
6. **External integrations.** Every third-party dependency, what it does, and the abstraction that isolates it.
7. **Infrastructure shape.** Where it runs, how it is deployed, what state lives where. Stays high-level; deployment detail belongs in operational documentation.
8. **Security posture.** The layers in place and what each covers. State the limits explicitly. A posture section that lists only strengths is read as a claim of completeness.
9. **Deferred decisions.** Covered below.
10. **Decision log.** Decisions made, numbered, each with date and reason.

Sections 9 and 10 carry most of the document's long-term value. The rest describes a state that can be read from the code. These two describe reasoning that cannot.

---

## Deferred decisions

The section most often omitted, and the one that pays.

Each entry records a decision consciously not made, plus the condition that will force it. Without this list, a deferral is indistinguishable from an oversight, including to the person who deferred it.

Entry format:

```
### D<nn> — <short title>

**Status:** Deferred
**Question:** What has not been decided.
**Current behaviour:** What the system does in the absence of a decision.
**Forcing condition:** What must happen before this must be decided.
**Options under consideration:** Brief, with the trade-off attached.
```

The forcing condition is the load-bearing field. "Decide later" is not a deferral, it is an unrecorded gap. "Decide before the first outlet outside the launch region" is a deferral with a trigger, and the trigger can be checked.

Numbering runs sequentially across the project and does not restart. A deferred decision that gets resolved keeps its number and changes status; it is not removed. Other documents cite these numbers.

---

## Abstraction boundaries

Where the architecture depends on an abstraction holding, say so and say what it isolates.

The common pattern: every external vendor sits behind an interface, and no service or controller imports a vendor library directly. The reason is that the vendor will be replaced, and the cost of replacement is bounded by the number of files importing it.

State each boundary as: what it wraps, what may import it, and what must never bypass it. The last part is what a static check can enforce and what belongs in the conventions file as a prohibition.

Boundaries stated without a corresponding prohibition erode. The architecture document explains why the boundary exists; the conventions file makes it enforceable.

---

## Recording a decision

When a deferred decision resolves, or a new decision is made:

1. Add or update the entry, with the date and the reason.
2. Where the decision creates a rule, add the rule to the conventions file. The architecture document is not read on every task.
3. Where the decision changes a spec, update the spec. Two documents describing the same thing differently is worse than one describing it incompletely.

A decision recorded in a chat session and nowhere else does not exist. It will be re-derived, differently.

---

## Maintenance

The document goes stale in a specific way: the stack section stays accurate because it is visible, and the reasoning sections rot because nothing contradicts them.

Review at the same cadence as the conventions file. Check three things:

- Does every deferred decision still have a live forcing condition?
- Does every abstraction boundary still exist in the code?
- Has any decision been made in practice without being recorded here?

The third is the common one. Decisions get made inside build steps and land in commit messages.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Stack choices without rejected alternatives | Choices read as defaults and get reversed casually. |
| Deferred decisions without forcing conditions | Indistinguishable from oversights. |
| Security section listing only what is in place | Read as a completeness claim. |
| Abstraction boundaries described but not prohibited in the conventions file | Boundaries erode without anyone noticing. |
| Renumbering sections | Breaks every citation from other documents. |
| Removing resolved deferrals instead of marking them resolved | Loses the record that the question was ever open. |
| Deployment detail in the infrastructure section | Duplicates operational docs and drifts. |

---

## Related

- `patterns/claude-md.md` for which decisions get promoted into the always-loaded conventions file.
- `patterns/divergence-log.md` for deviations that ship without changing the architecture.
- `patterns/build-sequence.md` for how deferred decisions map onto step ordering.
