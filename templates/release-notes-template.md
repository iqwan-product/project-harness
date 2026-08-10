<!--
TEMPLATE. Release notes.

Written for someone deciding whether to upgrade. That decision needs three
things: what is new, what breaks, and what to do about it.

A generated changelog is an acceptable starting point and not the deliverable.
Commit messages are written for the repository; these are written for users.

Reasoning in patterns/pr-and-release-writing.md. Delete every comment block.
-->

## <Version> — <Date>

<!-- One or two lines on the release, where it has a theme. Omit otherwise. -->

### Breaking

<!--
Always first. Each entry paired with the migration action.

A breaking change listed without a remedy is a support request.

Omit the heading entirely where there are none. Do not write "None".
-->

- **<Change>.** <What to do about it.>

### Added

<!--
Capabilities, in user terms.

Good: "Orders can now be scheduled up to seven days ahead."
Weak: "Added scheduledFor field to the order model."
-->

-

### Fixed

<!--
The symptom that is gone, not the code that changed.

Good: "Orders placed near midnight were assigned to the previous day."
Weak: "Corrected timezone handling in order creation."

The weak form requires the reader to work out whether they were affected.
-->

-

### Internal

<!--
Changes with no user-visible effect. Brief, or omitted entirely.

Where this section grows long it is being used to demonstrate activity, and
readers learn to skip the whole document.
-->

-
