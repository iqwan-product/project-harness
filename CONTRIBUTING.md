# Contributing

The kit records what has been run, not what sounds sensible.

## What earns a place

An addition must come from one of three sources.

**An incident.** Something failed, the cause is understood, and the
correction generalises past the project it happened in.

**A capability change.** The tooling moved, and an item is now wrong,
incomplete, or newly possible. Name what changed.

**A practice change.** A different approach was used across real work
and proved better than what the kit currently describes.

What does not qualify: anything not yet run. A plausible practice is
not evidence, and the kit's usefulness falls as its length grows.

Corrections specific to one stack belong in that project's own
documentation. The kit stays stack-agnostic: no language, framework,
database, or cloud provider.

## How to write it

Every item states what it prevents or enables, not only what to do. An
item without its reason cannot be assessed for applicability, so the
next reader drops it, correctly.

Register is flat technical reference. No first person, no narrative,
no marketing.

## Removals

Removals follow the same discipline and are equally welcome. Where an
item no longer earns its place, cut it and say why. Capability changes
produce as many removals as additions.

## Pull requests

Use the format in `templates/pr-description-template.md`. What
changed, why, how to verify, and what it could break.
