# Ticket Anatomy

Templates for the description body. One per kind. Read when composing or editing a ticket.

Angle brackets mark what the composer fills. Everything else is written as shown, including the headings, so the sections stay greppable across tickets.

---

## Shared conventions

**Summary line:** `[<step-id>] <title>`. The identifier is what every derived artifact is named after, so it leads.

**Context section:** an ordered list, numbered, contract document first. Each entry is a path or a page reference plus a short note on why it is read. Eight to ten entries. Where the step touches more than ten files, the extra ones belong in the execution prompt's file list, not the context section.

**Locked decisions:** numbered, inlined, stated flatly. These are the one place content is copied rather than pointed at, because a decision behind a pointer is a decision that gets re-derived.

**Derived paths:** compute from the step identifier and `docs_layout`. Never store a path the layout already determines.

**Empty sections stay.** A heading with `None` beneath it records that the question was asked. A missing heading records nothing.

---

## Code work

```
h2. What this is
<Two to four sentences, plain language. What gets made, roughly how.>

h2. Proves
<From the build sequence. What is true afterwards that was not before.>

h2. Delivers
<From the build sequence.>

h2. Out of scope
<From the build sequence. Where something is deferred, name the later step.>

h2. Context to read
# <contract document> — the contract; supersedes everything below
# <plan, where one exists>
# <agent definitions>
# <conventions file>
# <spec sections>
# <code files the step touches>

h2. Locked decisions
# <decision>
# <decision>

h2. Open questions
- [ ] <question> — ask, do not assume

h2. Artifacts to produce
- Rubric: <derived path>
- Plan: <derived path, Path A only>
- Draft pull request
- Verdict: <derived path>
- QA report: <derived path>

h2. Ready checklist
- [ ] Rubric exists and is readable
- [ ] Every context pointer resolves
- [ ] Locked decisions recorded
- [ ] Open questions resolved or accepted as open
- [ ] Execution prompt present
- [ ] Blocking tickets closed

h2. Execution prompt
<The orchestrator prompt, pointers throughout. Present here so it is read
before it is approved, not generated at the moment of handoff.>

h2. Updates
<Appended during work.>

h2. Deliverables
<Filled at close.>
```

---

## Documentation and non-code work

Shorter. No orchestrator, no rubric, no verdict. `agent-pattern.md` excludes documentation edits and investigation passes from the orchestrator pattern, so the artifacts it produces do not apply here.

```
h2. What this is
<Two to four sentences, plain language.>

h2. Proves
<From the build sequence.>

h2. Delivers
<The document, named, with where it lands.>

h2. Out of scope
<From the build sequence.>

h2. Context to read
# <source material>
# <the document this one supersedes or extends>
# <style or voice reference, where one applies>

h2. Locked decisions
# <decision>

h2. Open questions
- [ ] <question>

h2. Artifacts to produce
- <Document, with its destination>

h2. Ready checklist
- [ ] Every context pointer resolves
- [ ] Destination decided
- [ ] Open questions resolved or accepted as open
- [ ] Blocking tickets closed

h2. Execution prompt
<The writing prompt.>

h2. Updates

h2. Deliverables
```

---

## Gate and decision work

For steps whose output is an approval or a decision rather than an artifact. The checklist is the deliverable.

```
h2. What this is
<Two to four sentences. What is being decided or verified.>

h2. What this gates
<What cannot proceed until this closes. Name the steps.>

h2. Context to read
# <the material the decision rests on>

h2. Checks
- [ ] <check, phrased observably>
- [ ] <check>

h2. Open questions
- [ ] <question>

h2. Ready checklist
- [ ] Every check is observable rather than structural
- [ ] Every context pointer resolves
- [ ] Blocking tickets closed

h2. Updates

h2. Deliverables
<The decision, and where it was recorded.>
```

---

## Defects

Created ad hoc rather than from the sequence. No step identifier, so the summary carries a plain title.

```
h2. What happens
<Observed behaviour.>

h2. Expected
<Intended behaviour.>

h2. Where
<Step, component, or surface. Link the ticket that introduced it, where known.>

h2. Source
<Upstream issue URL, where the defect came from outside. One-directional:
this ticket records the issue, the issue records nothing about this ticket.>

h2. Updates

h2. Deliverables
```

---

## Deliverables section, at close

Same shape for every kind.

```
h2. Deliverables

*Pull request:* <URL, verified merged>

*What was done*
<Summary. Read by whoever picks up adjacent work.>

*Deviations*
<Each deviation, with its removal trigger. Logged to the divergence log.>
<Or: None.>

*Issues found, not fixed*
<Each issue, with the ticket it became.>
<Or: None.>

*Dependencies discovered*
<Each, with the sequence amendment or link it produced.>
<Or: None.>
```

The three sections after the summary are actions, not notes. Each one that is non-empty has somewhere else it must also land. Closing without taking those actions is how the same issue is rediscovered later as new work.
