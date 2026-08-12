# Gate Log

Difficulty: `easy`

Answer path: `internal/interview/dsa/easy/004-gate-log/`

A warehouse gate log uses `(` and `)` for visitor entry and exit, `[` and `]`
for vehicle entry and exit, and `{` and `}` for delivery entry and exit.
Determine whether a log is valid: every exit must match its event type and
close the most recent unmatched entry; no exit may occur before its matching
entry; and no entries may remain open at the end.

## Constraints

- Input contains only `(`, `)`, `[`, `]`, `{`, and `}`.
- The input may be empty.

## Examples

```text
([]{}) -> valid
([)] -> invalid
(() -> invalid
```
