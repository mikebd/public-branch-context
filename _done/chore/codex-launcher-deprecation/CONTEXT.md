# Branch Context: `chore/codex-launcher-deprecation`

## Scope

Remove the deprecated `codex/launcher/` subsystem from Bash Scripts. Replace
its generic command-check primitive with a reusable sourceable library at
`lib/requirements.sh`, then retain only the Bash Scripts capabilities that are
not specific to the legacy Codex launcher.

This context establishes the delivery boundary. It does not create or switch a
product branch, modify product files, or migrate external consumers.

## Working agreements

- Add `lib/requirements.sh` as a top-level sourceable library for common Bash
  helpers that can be used both inside this repository and by external scripts.
  Its generic API is `mikebd_require_command <command> [hint]`, with
  `mikebd_require_git` as the generic Git wrapper.
- Preserve the existing command-check success and diagnostic behavior while
  removing launcher-specific naming. Do not move Codex, jq, session-metadata,
  launcher-rendering, or launcher-configuration behavior into `lib/`.
- Update `git/worktree-links.sh` to source the generic requirement library and
  validate Git itself. External worktree-link adapters must then depend on the
  generic library rather than `codex/launcher/requirements.sh`.
- Remove `codex/launcher/` as a complete capability in the same Bash Scripts
  change that introduces `lib/requirements.sh`: implementation, README,
  launcher-specific tests, fixtures, portable-script inventory, and root
  documentation references all leave together.
- Do not retain compatibility shims, old function aliases, or a transition
  copy of `codex/launcher/requirements.sh`.
- Existing generated Bash launchers and external adapters may break after this
  change. That is an accepted compatibility boundary. Downstream consumers
  refactor as fast follows once the generic library lands; they are not removal
  blockers.
- Keep product and Branch Context changes separate. This context must not be
  referenced by product source or documentation.

## Downstream follow-up

The known consumers are Buds, the Buds `buds-workflow` worktree, Sharpen Blade,
and Sharpen Blade interview DSA tooling. Their follow-up is to source
`lib/requirements.sh` and use `mikebd_require_git` for generic worktree-link
setup. Their legacy launcher migration and any machine-local artifact handling
remain outside this Bash Scripts change.

Sharpen Blade commit `238463d` removes its visible repository-local Codex
launcher assistance but still sources `codex/launcher/requirements.sh` from
its worktree-link setup. Treat that as a fast-follow consumer refactor, not as
a reason to preserve the removed Bash Scripts interface.

## Completion outcome

Bash Scripts no longer contains a Codex launcher implementation. Its generic
Git worktree-link engine has a direct dependency only on the reusable `lib/`
requirement API, and downstream repositories can adopt that API independently.

## Completion

This completed context records delivery on product branch
`chore/codex-launcher-deprecation`. No WBS was created; the approved and
implemented plan is captured in `plans/001-codex-launcher-deprecation.md`.
