# Badge Scan

Difficulty: `easy`

Answer path: `internal/interview/dsa/easy/002-badge-scan/`

A building records badge IDs in the order they are scanned. Return the first
badge ID whose scan is a repeat of an earlier scan. If no badge ID repeats,
indicate that no repeated badge exists.

## Constraints

- Badge IDs are exact, case-sensitive strings.
- The input may be empty.
- The first repeat is the earliest scan event that repeats an earlier badge ID.

## Examples

```text
[A12, B07, A12, B07] -> A12
[A12, B07] -> no repeated badge
```
