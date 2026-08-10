# Three Tool Lanes

Reference for dividing work across Chat, Cowork, and Claude Code, and for routing document edits to the right one.

Three tools with overlapping capability. All three can reason about a change; two can write files; one owns git. Overlap is where the rework comes from, because a tool operating outside its lane produces output that looks correct and lands in the wrong state.

Projects using only Claude Code can skip this file. The routing table at the end still applies in reduced form.

---

## Lanes

| Lane | Owns | Never does |
|---|---|---|
| Chat | Decisions, planning, rubrics, prompt authoring, analysis | Edit files. Run git. |
| Cowork | Surgical multi-file document edits | Decide what to change. Run git. |
| Claude Code | All code, all file operations in the repository, all git | Decide scope unilaterally |

Two rules carry most of the value:

**Chat does not edit files.** Chat produces the instruction; something else applies it. A Chat session that edits files holds a picture of repository state that nothing verified, and that picture diverges silently.

**Claude Code does not decide what to edit.** It receives scope and executes. Where the scope is wrong, it flags rather than adjusts. A coding agent that expands scope on reasonable grounds produces changes nobody reviewed the decision for.

---

## The document edit flow

Three stages, in order, for any change to project documentation.

1. **Chat analyses and produces the instruction.** Either an inline edit block, a Cowork prompt, or a complete file.
2. **Cowork or a direct file write applies it.**
3. **Claude Code handles git.** Branch, commit, pull request.

The separation exists because these three stages fail differently. Analysis fails by being wrong about the current state. Application fails by landing something other than what was specified. Git operations fail by landing in the wrong place. Combining them makes the failure mode ambiguous.

---

## Routing

| Change shape | Route | Reason |
|---|---|---|
| One to three edits in one file | Inline before-and-after blocks in the chat reply | Cheapest. Applied by hand in seconds. |
| Four or more edits in one file | Cowork prompt | Volume where hand-application starts producing errors. |
| Edits across multiple files with an invariant that must hold consistently | Cowork prompt | The invariant is what Cowork enforces. A status change that must propagate identically across four documents is the case. |
| New file, or a rewrite exceeding half the file | Direct file output, overwrite in place | Half the tokens of a Cowork prompt that would embed the same content. |
| Document heavy in reasoning rather than structure | Direct file output, even for moderate edit counts | Reasoning-dense prose compresses under surgical editing. See below. |

**The anti-pattern:** wrapping an already-drafted file in a Cowork prompt as a whole-file replacement. Where the full target content already exists as an artifact, that artifact is the deliverable. Regenerating it inside a prompt duplicates the content for no gain.

---

## Verifying Cowork output

A Cowork confirmation that an edit was applied confirms that the find-and-replace executed. It does not confirm that the replacement landed verbatim.

Observed behaviour: behavioural directives survive intact. Explanatory prose, rationale, and trailing sentences compress. The result is an edit that is correct in instruction and lighter in reasoning than what was specified.

This matters most for specifications and rubrics, where the reasoning is the content. It matters least for status flips and reference updates, where the instruction is the content.

Verification step, run in Claude Code after any Cowork pass on a reasoning-heavy document:

1. Compare each specified replacement against what is in the file.
2. Classify each difference: verbatim, compressed, or missing.
3. Decide which compressions are acceptable and fix the rest.

Where a document is mostly reasoning, route it to direct file output instead and skip the verification cost entirely.

---

## Working location

Where Cowork edits the primary checkout while a build step runs in a worktree, the primary checkout ends up carrying uncommitted changes.

Two consequences:

- A prompt that opens a pull request from the primary checkout must end by discarding unrelated dirty state, or the pull request carries changes from a different piece of work.
- A review artifact written to the primary checkout instead of the worktree causes the next pull to fail cleanly-forwarding after a squash merge, because the local branch has content the remote does not.

Rule: work written during a build step goes in that step's worktree. Document edits go in the primary checkout, and the primary checkout stays on the default branch.

---

## Handoff artifacts

Each lane transition passes a specific artifact. Naming them makes an incomplete handoff visible.

| Transition | Artifact |
|---|---|
| Chat to Claude Code, build work | Orchestrator prompt, referencing a rubric that exists in the repo |
| Chat to Claude Code, investigation | Investigation prompt with named questions and an output location |
| Chat to Cowork | Edit prompt with find-and-replace pairs and a file list |
| Cowork to Claude Code | File list changed, so the git operation has explicit scope |
| Claude Code to Chat | Summary and a pointer, not a transcript |

Every handoff artifact is self-contained. A prompt requiring the recipient to splice in content by hand is an incomplete handoff, and the splice is where the error enters.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Chat editing files directly | Unverified state, silent divergence. |
| Claude Code deciding scope | Changes nobody reviewed the reasoning for. |
| Cowork prompt containing an already-drafted file | Duplicate content, double the tokens. |
| Trusting a Cowork confirmation as verbatim landing | Silent compression of reasoning. |
| Build-step artifacts written to the primary checkout | Merge and pull failures after squash. |
| Prompts requiring manual splicing | Introduces error at the splice point. |

---

## Related

- `patterns/agent-pattern.md` for the Chat-to-Claude-Code build handoff.
- `patterns/doc-structure.md` for where each artifact type lives.
