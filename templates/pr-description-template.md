<!--
TEMPLATE. Pull request description.

Applies from Gate C, once someone other than you reads these.

Under 300 words for a typical change. Evidence does not go here: inventory
output, rubric self-checks, phase tables, and command output go in a comment
or a linked file. Reasoning in patterns/pr-and-release-writing.md.

Delete every comment block.
-->

## What

<!--
One paragraph, in behaviour rather than files.

Good: "Adds phone-number verification to signup."
Weak: "Adds OtpService and wires it into the auth controller."

The implementation is in the diff. This says what the diff is for.
-->

## Why

<!--
The problem solved, or the step completed. Link the issue, step, or rubric.

Link rather than explain. Where the reasoning is long it belongs in the rubric
or the architecture doc, and this points at it.
-->

## How to verify

<!--
Three to six numbered steps a reviewer follows to see it working.

This is what makes review possible for someone who did not write the code.
Without it, most reviews become a read of the diff and an approval.
-->

1.
2.
3.

## Risk

<!--
What this could break, what is untested, and any divergence logged.

The section that establishes trust. A pull request naming what is untested
reads as considered. One naming nothing reads as either complete or
unexamined, and the reader cannot tell which.

Where there is genuinely no risk, say so and say why.
-->

---

<!--
Evidence goes in a comment below, not here. Suggested comment structure:

  ## Evidence
  ### Inventory
  ### Rubric self-check
  ### Phases and commits
  ### Exit demonstration output
-->
