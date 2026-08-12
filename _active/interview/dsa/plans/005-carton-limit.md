# Carton Limit

Difficulty: `easy`

Answer path: `internal/interview/dsa/easy/005-carton-limit/`

A delivery van has a load limit. Given a list of carton weights and a limit,
determine whether two different cartons can be selected whose combined weight
equals the limit.

## Constraints

- Carton weights and the limit are non-negative whole numbers.
- The list may be empty.
- A carton entry cannot be reused.
- Equal weights may form a pair when they belong to separate entries.

## Examples

```text
[4, 9, 2], 11 -> a pair exists
[3, 3], 6 -> a pair exists
[5], 10 -> no pair exists
```
