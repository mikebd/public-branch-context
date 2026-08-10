# Branch Context

This repository is the local Branch Context root for `go-sharpen-blade`.
Shared BC behavior comes from the authoritative Branch Context guidance; the
local instructions are in [`AGENTS.md`](AGENTS.md).

Workflow lanes are added only when the work requires them, using the standard
BC lane structure. The current active BC is
[`interview/dsa`](_active/interview/dsa/).

Approved explicit plans are stored inside the individual BC that owns them,
using a `plans/` subdirectory and the next available three-digit filename
sequence beginning at `001`, followed by a brief lowercase kebab-case summary
slug:

```text
_active/feat/feature-name/plans/001-brief-plan-summary.md
```

This applies only to decision-complete plans produced in explicit Plan Mode
and approved for implementation. Plans formed implicitly during ordinary
execution are not captured here.
