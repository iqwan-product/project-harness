# Transitions

The state model and the gate manifest. Read on any status change.

Status names and transition identifiers come from `configuration.md`. The names below are the roles, not the literal strings.

---

## State model

| State | Meaning | Gate |
|---|---|---|
| Unwritten | The ticket exists as a title and nothing else. | none |
| Ready | Complete as a ticket. Every section filled, every pointer resolves. | soft |
| In progress | Work has begun. | soft |
| In review | Converged. Awaiting the owner's decision to merge. | soft |
| Done | Merged, verified. | **hard** |
| Blocked | Cannot proceed. Carries its unblocking condition. | reason required |

Ready describes the ticket, not the schedule. A ticket can be complete months before its turn comes. Scheduling stays in the build sequence.

Blocked is reachable from any state and returns to the state it left.

---

## Gate manifest

Each check resolves against the world. Never against the ticket's own description.

### To Ready

| Check | How |
|---|---|
| Every context pointer resolves | Fetch each page, read each path |
| Rubric exists and is readable | Read the derived path. Code work only |
| Locked decisions section is non-empty | Read the ticket |
| Open questions resolved, or explicitly accepted as open | Read the ticket, ask |
| Execution prompt present | Read the ticket |
| Blocking tickets closed | Read each linked ticket's state, not this ticket's account of it |

Soft. Report the count, list the failures, proceed on instruction.

A ticket may go to Ready with open questions. Accepting them as open is a decision; leaving them unnoticed is not. The gate exists to force the first.

### To In progress

| Check | How |
|---|---|
| Ticket is in Ready | Read the tracker |
| Prerequisite steps are Done | Read each prerequisite's state |
| Working tree is clean | Query the repository |

Soft. Mirrors the orchestrator's own preconditions rather than replacing them; the orchestrator checks again at its start, which is correct. Two cheap checks in different places beat one check trusted in both.

### To In review

| Check | How |
|---|---|
| A pull request exists for this step | Query the host |
| Deterministic gate passed | Read the run record |
| Verdict file exists | Read the derived path |
| QA report exists | Read the derived path |

Soft. This state corresponds to the orchestrator's post-grader, post-QA checkpoint: converged, out of draft, awaiting the owner's instruction to merge.

### To Done

| Check | How |
|---|---|
| The pull request is merged | Query the host. Merged state, not approved, not ready |
| Deliverables section is filled | Read the ticket |
| Closing actions taken or declined | Confirmed in the close procedure |

**Hard. Refuse on failure.**

Done means merged, and every downstream precondition check reads that marker. A ticket marked Done on an unverified claim makes every later dependency check unsound, silently, and the failure surfaces at the step that trusted it.

Where the merge cannot be verified because the host is unreachable, the result is unknown, not pass. Refuse and say which check could not run.

### To Blocked

| Check | How |
|---|---|
| An unblocking condition is supplied, in words | Ask for it |

Refuse the transition until the condition exists. An open blocking link records that something blocks. It does not record what unblocks it, and without that sentence the state becomes permanent.

Write the condition into the ticket, not only into the transition comment.

---

## Verification, generally

| Claim | Acceptable evidence |
|---|---|
| A document exists | A successful read |
| A page exists | A successful fetch |
| A prerequisite is done | That ticket's state, read from the tracker |
| A pull request merged | The host's merged state |
| A check passed | The run record, not a summary of it |

Where a check cannot run, report unknown. An unverifiable gate that passes is worse than no gate, because something downstream trusts it.

---

## Lane recommendations

Configured in `configuration.md`. Stated, not enforced.

| Transition | Surface normally making it | Why |
|---|---|---|
| Stub, hydrate, to Ready | Planning | Composition and decision work |
| To In progress | Execution | Work having begun is a repository fact |
| To In review | Execution | Requires verdict and QA artifacts on disk |
| To Done | Execution, after the owner instructs the merge | Merge is a repository fact |
| To Blocked | Any | Blocking is discovered wherever it is discovered |

Where a transition is requested from a different surface, say which surface normally makes it and what that surface can verify that this one cannot. Then proceed on confirmation.

The reasoning is not territorial. A planning session cannot verify a merge, so a planning session recording one is recording a belief. That is the same failure the lane rules prevent elsewhere, arriving through a different door.
