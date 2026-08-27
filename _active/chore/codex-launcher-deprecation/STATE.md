# State: `chore/codex-launcher-deprecation`

## Status

Implementation is complete. Product commit `6601147` is pushed to
`origin/chore/codex-launcher-deprecation`; this Branch Context records its
delivery baseline.

## Evidence and findings

- Before removal, `codex/launcher/requirements.sh` owned
  `mikebd_launcher_require_command`, plus launcher-specific Git, jq, and Codex
  wrappers. Only the command check and generic Git wrapper belonged in the new
  reusable library.
- Before implementation, `git/worktree-links.sh` relied on callers to source
  legacy launcher requirements before invoking it. It now sources
  `lib/requirements.sh` and enforces its own Git prerequisite.
- Before removal, the root README presented `codex/launcher/` as a portable DX
  primitive and the portable-DX test runner executed launcher tests. Both
  references left with the subsystem.
- Buds and its `buds-workflow` worktree currently source the legacy
  requirements file for local DX worktree links. Sharpen Blade and its
  interview DSA tooling do the same.
- Sharpen Blade commit `238463d` removed its launcher adapters and local
  launcher test suite, but its remaining worktree-link command still sources
  the legacy requirements file. It will require the generic-library fast
  follow after this repository publishes the removal.

## Delivery sequence

1. Add `lib/requirements.sh`, its focused tests, and its source-isolation and
   portability coverage. Move the generic command and Git checks there.
2. Make `git/worktree-links.sh` source the new library and update internal
   tests and documentation accordingly.
3. Delete `codex/launcher/` and every Bash Scripts reference to it without
   retaining a compatibility path.
4. Refactor downstream worktree-link adapters as separate fast-follow changes
   to use `lib/requirements.sh` and `mikebd_require_git`.

## Acceptance evidence

- Focused library tests cover available commands, unavailable commands, and an
  optional diagnostic hint.
- Source-isolation and portability tests cover `lib/requirements.sh` and
  `git/worktree-links.sh`; launcher-only fixtures and tests are removed.
- A repository search finds no tracked `codex/launcher` references after the
  deletion.
- `bash -n`, ShellCheck, `tests/run-portable-dx.sh`, and `git diff --check`
  pass for the Bash Scripts change.

## Implementation update (2026-08-26)

- Added `lib/requirements.sh` with the generic command and Git requirement
  API, plus its API documentation and focused tests.
- Updated `git/worktree-links.sh` to load the generic library within its main
  function before running Git commands.
- Removed `codex/launcher/`, its test suite and fixtures, and every tracked
  product reference without retaining a compatibility path.
- Updated the portable-test inventory, source-isolation coverage, root and Git
  documentation, and repository agent guidance.
- Validation passed: `bash -n`, ShellCheck, `tests/run-portable-dx.sh`, a
  tracked-file launcher-reference audit, and `git diff --check`.
- Product delivery: `6601147 chore: remove deprecated codex launcher`, pushed
  to `origin/chore/codex-launcher-deprecation`.

## Next action

Downstream worktree-link adapters remain separate fast-follow changes.
