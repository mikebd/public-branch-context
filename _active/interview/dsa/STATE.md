# DSA interview practice state

Updated: 2026-08-11
Status: active
Next plan number: `003`
Current question: `002-badge-scan`

## Current state

- Administrative setup is captured in
  `plans/001-initialize-dsa-interview-workflow.md`.
- Question `002-badge-scan` has been evaluated at the `easy` difficulty.
- The review found correct earliest-repeat behavior for ordinary badge IDs;
  focused tests pass. The current implementation distinguishes a repeated
  empty badge ID from no repeated badge.
- The current implementation and tests are not fully staged; stage them before
  committing so the fixed return shape and empty-ID case are included.

## Question ledger

The ledger starts with question `002`. Add a row when a question is issued,
but leave its concepts undisclosed until the answer has been evaluated.

| Plan | Difficulty | Status | Answer path | Concepts after evaluation | Strengths and gaps |
| --- | --- | --- | --- | --- | --- |
| 002 | easy | evaluated | `internal/interview/dsa/easy/002-badge-scan/` | single-pass scan; prior-value membership | Correct earliest-repeat behavior, no-result signaling, and empty-ID coverage; add explicit nil/case-sensitive tests and stage the implementation/test updates before committing. |

Statuses progress as applicable through `issued`, `evaluated`,
`follow-up-issued`, and `follow-up-evaluated`.
