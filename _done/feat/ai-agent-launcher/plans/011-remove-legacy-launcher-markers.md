# Remove legacy launcher markers

## Summary

Keep metadata format version `1`, with no ADR change. New launchers omit marker
comments and metadata; existing v1 metadata may retain a `marker` key that is
silently ignored and discarded on rewrite.

## Implementation changes

- Remove `--marker` from `launcher create`, `worktree new`, and `worktree
  stack`; reject old flag usage normally.
- Remove marker state, validation, rendering, description output, and
  lifecycle/worktree API parameters.
- Keep v1 parsing strict for all execution-relevant fields and unknown keys,
  but allow the one legacy `marker` key with any JSON value. Do not validate,
  expose, preserve, or act on it.
- Do not rewrite during `run` or `describe`; lifecycle operations that render a
  launcher canonicalize it without the marker.
- Remove marker references from ordinary documentation and add the v0.1.2
  release-note compatibility note.

## Validation

- Verify newly generated launcher shims, metadata, help, and completion have
  no marker support.
- Verify old v1 artifacts work with no marker or arbitrary marker values, and
  are marker-free after a rewrite.
- Preserve rejection coverage for unsupported versions, absent execution
  state, and unknown metadata keys.
- Run `make all` and `make release-check`.

## Defaults

- No v2 metadata, migration command, bulk rewrite, or ADR.
- The compatibility allowance is limited to the known v1 `marker` key; it does
  not establish a general unknown-field policy.
