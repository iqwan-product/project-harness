# Configuration

The skill holds no site, project, or status identifiers. Every value below is supplied by the adopting project, in a file the project owns.

Location: `docs/work-items-config.md`, or wherever the project's conventions file points. Where the file is absent, stop and ask. Do not read the values out of the tracker and proceed; inferred configuration is plausible, unreviewed, and wrong in the places that matter.

---

## Required values

| Key | What it is |
|---|---|
| `cloud_id` | Atlassian site identifier |
| `project_key` | Jira project key |
| `types.grouping` | Issue type for the grouping level |
| `types.code` | Issue type for code work |
| `types.docs` | Issue type for documentation and non-code work |
| `types.defect` | Issue type for defects |
| `statuses` | Each state, with its status name and transition identifier |
| `labels` | The label scheme, with its prefixes |
| `docs_layout` | Directory and naming convention for derived artifact paths |
| `sequence_doc` | Where the build sequence lives |
| `key_in_vcs` | `forbidden` or `allowed` |
| `lanes` | Which surface normally makes each transition |

---

## Shape

```yaml
cloud_id: <uuid>
project_key: <KEY>

types:
  grouping: Epic
  code: Story
  docs: Task
  defect: Bug

statuses:
  unwritten:   { name: "To Do",       transition_id: "<id>" }
  ready:       { name: "Ready",       transition_id: "<id>" }
  in_progress: { name: "In Progress", transition_id: "<id>" }
  in_review:   { name: "In Review",   transition_id: "<id>" }
  blocked:     { name: "Blocked",     transition_id: "<id>" }
  done:        { name: "Done",        transition_id: "<id>" }

labels:
  group:  "<prefix>-"     # grouping membership
  kind:   "kind-"         # code | docs | gate
  where:  "where-"        # which surface executes
  path:   "path-"         # planning path, where the sequence records one
  step:   "step-"         # step identifier

docs_layout:
  rubrics:  "docs/rubrics/<step-id>-rubric.md"
  plans:    "docs/plans/<step-id>-implementation-plan.md"
  verdicts: "docs/verdicts/<step-id>-grader-verdict.md"
  qa:       "docs/qa/<step-id>/"
  divergences: "docs/DIVERGENCES.md"

sequence_doc: <path or page reference>

key_in_vcs: forbidden

lanes:
  stub:                   planning
  hydrate:                planning
  to_ready:               planning
  to_in_progress:         execution
  to_in_review:           execution
  to_done:                execution
  to_blocked:             any
```

---

## Notes on individual values

**`transition_id`** is not the status identifier. Read them from the tracker once, against a real ticket, and record them. They are stable for the project's lifetime and re-reading them on every call wastes a round trip.

**`labels`** carry what the tracker cannot express natively. Where the project is team-managed, components are usually unavailable and labels are the only grouping mechanism below the hierarchy level. Keep prefixes short; they appear on every ticket.

**`docs_layout`** exists so artifact paths are derived rather than stored. Where the project does not prefix filenames with the step identifier, paths cannot be derived and must be written into each ticket, which is a maintenance cost worth avoiding at the layout level instead.

**`key_in_vcs: forbidden`** means the ticket key must never appear in a branch name, commit message, pull request title, or pull request body. Under this setting, branch names derive from the step identifier, and the tracker's native development panel will stay empty. That is expected, not a misconfiguration.

**`lanes`** are recommendations. The skill states the configured surface when a transition is requested elsewhere, then proceeds on confirmation. Set every value to `any` to disable the recommendation entirely.

---

## Verification

Before the first run, confirm each of these against the tracker once:

- [ ] Every status in `statuses` exists, spelled as configured.
- [ ] Every `transition_id` returns the expected target status.
- [ ] Every issue type in `types` exists and is available on the project.
- [ ] The grouping type sits above the others in the hierarchy.
- [ ] The link type used for dependencies exists.

A wrong transition identifier fails loudly. A wrong status name fails quietly, by creating nothing and reporting success.
