# Pull Request and Release Writing

Reference for writing pull request descriptions, commit messages, and release notes once someone other than the author reads them.

Applies from Gate C onward. Before that gate, a pull request description is a note to yourself and its shape does not matter much.

---

## The problem this addresses

Agent-driven workflows route bulk output into the pull request body deliberately. It keeps the orchestrating session's context small, and it puts the audit trail where the reviewer already is. Both reasons remain valid.

The result is a description that opens with an inventory, a rubric self-check, a phase-to-commit table, and command output. It is complete and it is not written for a person.

The correction is placement, not deletion. Evidence still has to exist and still has to be attached to the pull request. It moves out of the first thing a reader sees.

---

## The split

| Content | Location |
|---|---|
| What changed and why | Pull request description |
| How to verify it | Pull request description, brief |
| Risk, and what could break | Pull request description |
| Inventory output | Comment, or a linked file |
| Rubric self-check | Comment, or a linked file |
| Phase-to-commit table | Comment |
| Command output, logs, test transcripts | Comment, or a linked file |
| Grader verdict | Separate documentation-only pull request |

A reader opening the pull request should be able to decide within a paragraph whether this change concerns them. Everything else is one click away.

---

## Description structure

Four sections. Under 300 words for a typical change.

```
## What

One paragraph. The change, in terms of behaviour rather than files.

## Why

The problem this solves, or the step this completes. Link the issue
or step where one exists.

## How to verify

Numbered, three to six steps. What a reviewer does to see it working.

## Risk

What this could break, what is untested, and any divergence logged.
```

**What** is written in behaviour, not implementation. "Adds phone-number verification to signup" rather than "adds OtpService and wires it into the auth controller". The implementation is in the diff.

**Why** links rather than explains. Where the reasoning is long, it belongs in the step's rubric or the architecture document, and the pull request points at it.

**How to verify** is what makes a review possible for someone who did not write the code. Where verification requires the reviewer to derive the steps from the diff, most reviews become a read of the diff and an approval.

**Risk** is the section that establishes trust. A pull request that names what is untested reads as a considered change. One that names nothing reads as either complete or unexamined, and the reader cannot tell which.

---

## Register

Flat and factual. Present tense, active voice.

| Avoid | Use |
|---|---|
| "This PR introduces a comprehensive refactor" | "Replaces the session store with a shared cache" |
| "Fixed some issues with the auth flow" | "Fixes token refresh returning 401 after the first retry" |
| "Various improvements" | Name them, or split the pull request |
| Emoji as section markers | Headings |
| Restating the diff line by line | Let the diff carry it |

State what is not covered. A description claiming completeness that a reviewer then disproves costs more trust than the omission would have.

---

## Commit messages

Conventional prefixes, imperative mood, one logical change per commit.

```
<type>(<scope>): <summary under 72 characters>

<Body, where the summary is insufficient. Wrapped at 72.>

<Footer: issue references, breaking-change notes.>
```

Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`.

One commit per phase of work, where the build pattern defines phases. Each commit leaves the tree passing its checks. A commit that does not build is a commit nobody can bisect through.

The summary line completes the sentence "this commit will...". `feat(auth): add phone verification`, not `feat(auth): added phone verification` or `feat(auth): phone verification stuff`.

---

## Release notes

Written for someone deciding whether to upgrade. That decision needs three things: what is new, what breaks, and what to do about it.

```
## <Version> — <Date>

### Breaking

- <Change>. <What to do about it.>

### Added

- <Capability, in user terms.>

### Fixed

- <The symptom that is gone, not the code that changed.>

### Internal

- <Changes with no user-visible effect. Brief or omitted.>
```

Breaking changes first, always, and each one paired with the migration action. A breaking change listed without a remedy is a support request.

**Fixed** entries describe the symptom, not the fix. "Orders placed near midnight were assigned to the previous day" is useful. "Corrected timezone handling in order creation" requires the reader to work out whether they were affected.

**Internal** is optional and short. Where it grows long, it is being used to demonstrate activity, and readers learn to skip the whole document.

Generated changelogs from commit messages are acceptable as a starting point and not as the deliverable. Commit messages are written for the repository; release notes are written for users.

---

## Anti-patterns

| Pattern | Consequence |
|---|---|
| Evidence at the top of the description | Readers cannot find the change. |
| Implementation-language summaries | Requires reading the diff to understand the purpose. |
| No verification steps | Reviews become diff reads. |
| Empty or absent risk section | Reader cannot distinguish complete from unexamined. |
| Multiple unrelated changes in one pull request | Cannot be reviewed or reverted independently. |
| Commits that do not build | Bisect becomes useless. |
| Release notes listing code changes rather than symptoms | Readers cannot tell whether they were affected. |
| Breaking changes without migration actions | Every affected user files the same question. |

---

## Related

- `templates/pr-description-template.md`
- `templates/release-notes-template.md`
- `patterns/agent-pattern.md` for where evidence is produced and where it belongs.
- `patterns/divergence-log.md` for what the risk section references.
