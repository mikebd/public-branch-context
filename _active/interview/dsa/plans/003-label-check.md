# Label Check

Difficulty: `easy`

Answer path: `internal/interview/dsa/easy/003-label-check/`

A warehouse label is accepted when its letters and digits, after ignoring all
other characters and treating uppercase and lowercase letters as the same,
read identically from left to right and right to left. Determine whether a
label is accepted.

## Constraints

- Input contains printable ASCII characters.
- Ignore punctuation and whitespace.
- Compare digits exactly.
- A label with no letters or digits is accepted.

## Examples

```text
A12-21a -> accepted
A12-31a -> rejected
```
