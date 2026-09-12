# Work Item Handoff

Reference for using a tracker as the handoff surface between planning and execution: what a work item must carry for an agent to start cold, which fields belong to the tracker rather than the build sequence, and what gates a status change.

A tracker used for project management records that work exists. A tracker used for handoff records what the work needs in order to start. The second is a smaller document with a harder requirement: an agent reading it, with no memory of the session that planned it, has to arrive at the same scope the planner intended.

Optional. Projects running a single session per step, or short enough that planning and execution share a context, do not need one.

---

## What the work item is for

One build sequence step becomes one work item. The step says what the work is. The work item says what an executor needs to begin it, and later, what it produced.

Three things follow.

**The work item does not restate the plan.** Where a field already exists in the build sequence, the work item copies it once at creation and never diverges from it afterwards. Where the two disagree, the sequence wins and the work item is stale.

**The work item holds mutable state.** Status, blocking reason, updates, and outputs change during the work. Those belong in the tracker, where they are timestamped and attributed, not in a specification that is reviewed on merge.

**Not every step needs one.** Steps executed in a single session, with no handoff, gain nothing from a work item and cost a maintenance burden. Create them for work that crosses a session boundary or a tool boundary.

---

## Pointers, not copies

The work item names the documents an executor must read. It does not contain them.

Embedded content is a copy taken at the moment the item was written. The source moves; the copy does not; nothing reports the difference. An executor reading a stale copy produces work against a superseded spec and the failure surfaces at review, or later.

A pointer fails differently and better. A page that no longer exists is detectable before work starts. A path that no longer resolves is a precondition failure rather than a wrong build.

Two exceptions, both deliberate:

| Content | Why it is inlined |
|---|---|
| Locked decisions | The executor's context must carry them without a read. A decision behind a pointer is a decision that gets re-derived. |
| Scope boundary | What the step excludes exists nowhere else at the right granularity. |

Where the documentation layout prefixes filenames with the step identifier, most pointers are derivable from the identifier alone and need not be stored at all. Storing what can be computed adds a second thing to keep correct.

---

## Ordered reads

The context section is an ordered list, not a set of links.

The contract document comes first. Everything after it is read in the knowledge that the contract supersedes it. Reversing that order produces an executor that forms a position from a specification section and then reads the contract as a constraint on a plan it has already made.

Keep the list to the files the step actually touches. A context section naming the whole documentation tree is a context section nobody follows.

---

## Required sections

| Section | Contents |
|---|---|
| Summary | Step identifier and title. The identifier is what every other artifact is named after. |
| What this is | Two to four sentences in plain language. What gets made, roughly how. |
| Proves | Copied from the build sequence. What is true afterwards that was not before. |
| Delivers | Copied from the build sequence. What ships. |
| Out of scope | Copied from the build sequence. Named exclusions, with the later step where something is deferred. |
| Context to read | The ordered list. Contract first. |
| Locked decisions | Numbered, inlined, stated so they need no re-derivation. |
| Open questions | Flagged as ask rather than assume. Unresolved items are visible before work starts, not discovered during it. |
| Artifacts to produce | Named outputs, by path where the layout determines them. |
| Ready checklist | What must exist before handoff. Advisory. |
| Execution prompt | The prompt the executor receives, pointers throughout. |
| Updates | Appended during work. Timestamped by the tracker. |
| Deliverables | Filled at close. |

The execution prompt sits in the item rather than being generated at handoff so that it can be read before it is approved. A prompt generated at the moment of execution is a prompt nobody reviewed.

---

## Status and gates

Five states, plus a blocked state reachable from any of them.

| State | Meaning | Gate |
|---|---|---|
| Unwritten | The item exists as a title. Nothing else. | none |
| Ready | The item is complete as an item. Every section filled, every pointer resolves. | soft |
| In progress | Work has begun. | soft |
| In review | Converged, awaiting the owner's decision. | soft |
| Done | Merged, verified. | hard |
| Blocked | Cannot proceed. Names its unblocking condition. | requires a reason |

**Ready means the item is ready, not that the work is next.** An item can be fully written months before its turn. Conflating the two makes Ready a scheduling signal, and scheduling belongs in the sequence.

**Soft gates report and proceed.** They list what is missing and continue on the owner's instruction. A gate that blocks on every unmet item gets bypassed, and a bypassed gate teaches that all gates are advisory.

**The done gate refuses.** Done means merged, and every downstream dependency check is built on that marker. An item marked done without verified evidence makes every later precondition check unsound. This is the one place where refusing is correct.

**Blocked carries text.** An open blocking link records that something blocks; it does not record what unblocks it. Without the sentence, the state becomes permanent.

---

## Verify rather than assert

A gate that reads the work item's own claims checks nothing. The item says a pull request merged because an agent wrote that it did.

Every gate item resolves against the world:

| Claim | Check |
|---|---|
| The contract document exists | Read the path |
| A prerequisite is done | Read the prerequisite's state, not this item's account of it |
| A pull request merged | Query the host |
| A pointer resolves | Fetch it |

Where a check cannot run, the correct result is unknown rather than pass. An unverifiable gate that passes is worse than no gate, because it is trusted.

---

## Closeout

The closing note is the carry-forward context for whatever comes next. Four parts, and three of them are actions rather than notes.

| Part | Where it goes next |
|---|---|
| What was done | Stays in the item. Read by whoever picks up adjacent work. |
| Deviations from the contract | The divergence log, with a removal trigger. |
| Issues found and not fixed | New work items, linked. |
| Dependencies discovered | Amendments to the sequence, and links on the affected items. |

Recording the last three only in the closing note is how they are lost. The item closes, nothing reads it again, and the same issue is rediscovered later as new. Close by offering each non-empty part its onward action.

---

## Keys and version control

Where a repository is public, or will become public, decide before the first commit whether tracker identifiers may appear in it.

Where they may not, the linkage is one-directional: the work item records the pull request, and the repository records nothing. Branch names derive from the step identifier rather than the tracker key.

This forfeits the automatic linking most trackers offer, which depends on the key appearing in branch names or commit messages. The executing agent writes the link into the item instead, which costs one call and keeps the direction clean.

---

## Lanes

Where a project divides work across tools, the division extends to the tracker on the same reasoning: a tool that cannot verify a fact should not record it.

A planning session cannot verify a merge. An executing agent can. That makes the terminal transition the executing agent's, not because the boundary is worth defending for itself, but because it is the same failure the lane rules already prevent elsewhere.

Recommend rather than enforce. The recommendation is stated when a transition is requested from an unexpected surface, and the owner overrides it by saying so. A hard rule here produces a tracker the owner cannot correct by hand.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Specification content copied into the work item | Silent staleness when the source moves. |
| Context section as an unordered link list | The contract is read after the documents it supersedes. |
| Gate items read from the item's own claims | The gate passes on an assertion nobody checked. |
| Done reachable without verified evidence | Every downstream precondition check is unsound. |
| Blocked without a named unblocking condition | Permanent blocked state. |
| A work item for every line of the sequence | Maintenance burden on steps that never cross a boundary. |
| Closing notes holding actions that were never taken | Issues rediscovered later as new. |
| Ready used as a scheduling signal | Scheduling leaves the sequence and enters the tracker. |
| Execution prompt generated at handoff | The prompt is approved without being read. |

---

## Related

- `patterns/build-sequence.md` for the step format the work item is built from.
- `patterns/agent-pattern.md` for the orchestrator preconditions a ready item should satisfy.
- `patterns/divergence-log.md` for where closing deviations are recorded.
- `patterns/three-tool-lanes.md` for the lane reasoning the transition recommendation extends.
- `skills/jira-work-items/` for one implementation of this pattern.
