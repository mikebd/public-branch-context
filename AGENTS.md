# Branch Context guidance for go-sharpen-blade

This directory is the repo-local Branch Context (`BC`) root for the
`go-sharpen-blade` repository. The shared Branch Context guidance remains
authoritative; this file records the repository-specific overlay.

## BC topology

- Keep branch-scoped BC under the standard workflow lanes when lanes are
  explicitly created.
- Do not create initial lanes automatically. The repository owner will add
  lanes as needed.
- Do not store secrets in BC.

## Approved BC plans

Unless a more specific instruction or an explicit user override says
otherwise, every approved explicit plan created for a BC must be captured in
that individual BC's `plans/` directory.

Use the next available three-digit sequence and a brief lowercase kebab-case
slug:

```text
_active/feat/feature-name/plans/001-brief-plan-summary.md
_active/feat/feature-name/plans/002-next-plan-summary.md
```

Plan numbering starts at `001` within each individual BC, is monotonic, and
must not reuse or overwrite an existing number. The slug should summarize the
plan rather than name a branch or session. Capture the decision-complete plan,
including its scope, implementation decisions, validation, and assumptions.
Only a decision-complete plan produced in explicit Plan Mode and then approved
for implementation needs to be recorded. Plans formed implicitly during
ordinary execution, without an explicit Plan Mode plan and approval, are not
included. Exploratory or otherwise unapproved planning conversation is also
not recorded.
