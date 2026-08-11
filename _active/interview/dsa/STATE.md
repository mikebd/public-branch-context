# DSA interview practice state

Updated: 2026-08-11
Status: active
Next plan number: `004`
Current question: `003-label-check`

## Current state

- Administrative setup is captured in
  `plans/001-initialize-dsa-interview-workflow.md`.
- Question `002-badge-scan` has an additional `easy` follow-up evaluated.
- The review found correct earliest-repeat behavior for ordinary badge IDs;
  focused tests pass. The current implementation distinguishes a repeated
  empty badge ID from no repeated badge.
- The follow-up correctly returns the zero-based repeat position with the
  repeated badge and uses the presence result for the no-repeat case.
- The second follow-up correctly returns both the original and repeat scan
  positions for the first repeated badge.
- Question `003-label-check` has been evaluated at the `easy` difficulty.
- The implementation correctly normalizes printable ASCII labels and accepts
  both even- and odd-length mirrored content.

## Question ledger

The ledger starts with question `002`. Add a row when a question is issued,
but leave its concepts undisclosed until the answer has been evaluated.

| Plan | Difficulty | Status | Answer path | Concepts after evaluation | Strengths and gaps |
| --- | --- | --- | --- | --- | --- |
| 002 | easy | follow-up-evaluated | `internal/interview/dsa/easy/002-badge-scan/` | single-pass scan; prior-value membership; first-occurrence positions | Correct earliest-repeat behavior, no-result signaling, empty-ID coverage, case-sensitive matching, and both zero-based positions. |
| 003 | easy | evaluated | `internal/interview/dsa/easy/003-label-check/` | character normalization; mirrored-sequence comparison | Correct punctuation, case, digit, and even/odd mirrored-label handling; optionally add a rejected digit-containing case. |

Statuses progress as applicable through `issued`, `evaluated`,
`follow-up-issued`, and `follow-up-evaluated`.
