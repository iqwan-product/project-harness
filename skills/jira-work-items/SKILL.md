---
name: jira-work-items
description: >
  Create, hydrate, gate, transition, and close Jira work items that carry
  enough context for an agent to start a build step cold. Use when the request
  is to turn a build sequence step into a ticket, fill in or hydrate an
  existing ticket, check whether a ticket has what its next status requires,
  move a ticket to Ready, In Progress, In Review, Blocked or Done, write a
  closeout note, or stub a set of tickets from a scope document. Also use when
  a build step is about to be handed to an orchestrator and no ticket exists
  for it. Do not use for reporting or querying across tickets, for editing
  Confluence pages, for writing the rubric itself, or for authoring the
  orchestrator prompt; those are separate tasks with their own guidance. Hard
  trigger: any request to change a work item's status.
---

# Jira Work Items

## Scope

This skill governs the lifecycle of a Jira work item used as a handoff artifact: one build sequence step becomes one ticket carrying the ordered reads, locked decisions, scope boundary, and execution prompt an agent needs to start without the planning session's context. It covers creation, hydration, gated transitions, and closeout. It does not cover reporting, rubric authoring, or Confluence editing.

Read `references/configuration.md` first. This skill carries no project-specific values; every identifier comes from the adopting project's configuration file. Where that file is absent, stop and ask for it rather than inferring values from the tracker.

---

## Rules

**Never write content the source already holds.** Point at documents. Copy only the fields the build sequence defines for the step, plus locked decisions and scope boundary, which are inlined deliberately. Where a documentation layout prefixes filenames with the step identifier, derive paths rather than storing them.

**Order the context section.** Contract document first, then plan where one exists, then agent definitions, then conventions, then the specific spec sections and code files. Never present the reads as an unordered list.

**Verify every gate item against the world.** Read the path, fetch the page, query the pull request host. Never accept the ticket's own description as evidence for a gate on that ticket. Where a check cannot run, report unknown, never pass.

**Confirm before writing.** Show the full description, transition, or edit and wait for approval. This applies to every create, edit, transition, and link.

**Report gate failures, then proceed on instruction.** Every gate except Done is advisory. List what is missing, state the count, ask. Do not refuse.

**Refuse Done without verified merge evidence.** This is the single hard gate. A ticket marked Done without a verified merged pull request makes every downstream precondition check unsound. Say so and stop.

**Blocked requires a written unblocking condition.** Refuse the transition until one is supplied. An open blocker link is not a condition.

**Honour the key policy.** Where configuration forbids tracker keys in version control, never write a ticket key into a branch name, commit message, pull request title, or pull request body, and never suggest a convention that would. Linking is one-directional: the ticket records the pull request URL, the repository records nothing.

**Recommend lanes, do not enforce them.** Where a transition is requested from a surface other than the one configured for it, say which surface normally makes it and why, then proceed if the owner confirms.

---

## Procedures

### 1. Stub

For creating many tickets from a scope document at once.

1. Read the scope document. Identify every item and its grouping.
2. Create the grouping tickets first, as the configured hierarchy type. Record the returned keys.
3. Create one ticket per item: summary as `[<step-id>] <title>`, configured type by kind, parent set to its grouping ticket, labels per the configured scheme. No description body.
4. Leave every ticket in the initial status.
5. Return the full key-to-item mapping in one block, so it can be written back to the scope document in a single pass.

Do not hydrate during a stub run. Stub tickets describe work whose specifications may not be settled.

### 2. Hydrate

For writing one ticket in full. Ends at Ready.

1. Read the build sequence entry for the step.
2. Read the documents the step depends on. Where a needed document does not exist, record it as an open question rather than inventing its content.
3. Derive artifact paths from the step identifier and the configured documentation layout.
4. Compose the description from `references/ticket-anatomy.md`, selecting the template for the ticket kind.
5. Create the dependency links the step's depends-on field names.
6. Show the composed description in full. Wait for approval.
7. Write it, apply labels, transition to Ready.

Hydration sets Ready because Ready means the ticket is complete as a ticket. It does not mean the work is next.

### 3. Transition

For any status change.

1. Read the ticket's current state from the tracker.
2. Load the gate for the target status from `references/transitions.md`.
3. Run every check against the world. Never against the description.
4. Report each check as pass, fail, or unknown, with the count.
5. Where the target is Done and merge evidence is absent or unverified, refuse and stop.
6. Where the target is Blocked and no unblocking condition was supplied, ask for one and stop.
7. Otherwise ask whether to proceed. On confirmation, transition using the configured transition identifier.

Where the requesting surface differs from the configured one for this transition, state that before step 7, then continue.

### 4. Close

For finishing a ticket. Runs before the Done transition, not after.

1. Collect the four closing parts: what was done, deviations from the contract, issues found and not fixed, dependencies discovered.
2. Write them into the ticket's Deliverables section along with the verified pull request URL.
3. For each non-empty part beyond the first, offer its onward action:
   - deviations, to the divergence log with a removal trigger
   - issues, as new tickets, linked
   - dependencies, as sequence amendments and links on the affected tickets
4. Take only the onward actions the owner confirms.
5. Run the Done transition per procedure 3.

Step 3 is the reason this procedure exists. A closing note whose actions are never taken is how the same issue is rediscovered later as new work.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Embedding specification text in the description | Silent staleness when the source moves. |
| Gate checks read from the ticket description | The gate passes on an assertion nobody verified. |
| Transitioning to Done on a claimed merge | Every downstream precondition check becomes unsound. |
| Hydrating during a stub run | Context written against specifications that have not settled. |
| Writing a ticket key into a branch or commit where configuration forbids it | Internal tracking leaks into a public repository, irreversibly. |
| Storing paths the step identifier already determines | A second thing to keep correct. |
| Unordered context section | The contract is read after the documents it supersedes. |
| Refusing a soft gate | The owner routes around the skill, and every gate loses force. |
| Taking onward actions at close without confirmation | Tickets and log entries nobody asked for. |
| Inferring configuration values from the tracker | Values that are plausible, unreviewed, and wrong. |

---

## Reference pointers

| File | Read when |
|---|---|
| `references/configuration.md` | Always, first. The values this skill needs and where the project supplies them. |
| `references/ticket-anatomy.md` | Composing or editing a description. Holds one template per ticket kind. |
| `references/transitions.md` | Any status change. Holds the state model and the gate manifest. |

---

## Related

- `patterns/work-item-handoff.md` for the reasoning behind this skill's structure.
- `patterns/build-sequence.md` for the step format tickets are built from.
- `patterns/agent-pattern.md` for the orchestrator preconditions a Ready ticket should satisfy.
