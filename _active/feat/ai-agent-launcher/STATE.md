# State

## Current status

Active as of 2026-08-23. The product branch and active BC lane use
`feat/ai-agent-launcher`; the product branch is published. WP-001 through
WP-004 and WP-009 are complete; WP-005 is in progress.

## Evidence

- WP-001 adds the `ai-agent-launcher` console command, agent-neutral
  identifiers and session references, an immutable internal adapter registry,
  and the identity-only built-in `codex` adapter.
- WP-001 was published as product commit `19fe3d6` on
  `origin/feat/ai-agent-launcher`.
- WP-002 adds TOML-backed Codex runtime dispatch, strict configuration
  validation, ordered writable-root resolution, direct process execution, and
  read-only internal session metadata discovery. It deliberately defers
  worktree lifecycle and generated launchers. `make all` passed (66 tests),
  and `uv run ai-agent-launcher --help` plus `--version` passed.
- WP-003 adds versioned generated launchers, lifecycle management, and explicit
  Codex migration while deferring Git worktree creation and stacking. `make
  all` passed with 73 tests; launcher and migration CLI help were checked.
- WP-004 adds generic worktree creation and strict sibling stacking, core
  launcher-directory configuration, preflight collision checks, and owned
  rollback. `make all` passed with 83 tests; the installed worktree CLI help
  was checked.
- WP-005 has completed the one-time private replacement of the three legacy
  local launchers, preserving two pinned sessions and one unpinned launcher.
  Its release candidate removes the migration surface, moves launcher-run
  rendering into the runtime adapter, adds release guidance, and passes `make
  release-check` (Ruff, Pyright, 84 tests, source build, and isolated temporary
  Git-tag installation).
- WP-005 also updates package metadata to the current PEP 621/SPDX form,
  preserves `LICENSE` in distributions, and removes the deprecated license
  classifier. `make release-check` completes without setuptools metadata
  deprecation notices.
- Fake adapter contract coverage now includes generic session fork and adoption
  delegation, generated target metadata, and cross-worktree adoption rejection.
- Public documentation now describes testing the current untagged checkout and
  an isolated local tool installation that leaves the normal launcher intact.
- WP-009 captures static completion generation from the existing `argparse`
  parser. It uses `shtab` for bash, fish, tcsh, and zsh, detects `$SHELL` only
  when no explicit shell is selected, and leaves completion installation to
  the user.
- WP-009 is complete. `make all` and `make release-check` passed with 96
  pytest tests, and the generated zsh script passed `zsh -n`.
- Plan 007 is complete and intentionally unassociated with a WBS work
  package. It establishes repository ADR guidance and records the accepted
  static shell-completion decision without changing completion behavior.
  `git diff --check` and `make all` passed with 96 pytest tests.
- Plan 008 is complete and intentionally unassociated with a WBS work
  package. It corrects ADR framing before the first ADR commit without
  changing product behavior. `git diff --check` and `make all` passed with 96
  pytest tests.
- Plan 009 is complete and intentionally unassociated with a WBS work
  package. It establishes scoped `ai-agent-launcher` decision records and
  nested guidance while keeping `docs/adr/` repository-wide. `git diff
  --check` and `make all` passed with 97 pytest tests.
- Plan 010 is complete and intentionally unassociated with a WBS work
  package. It separates direct untagged-checkout CLI smoke tests from the
  installed PATH contract for persistent launchers. `git diff --check`,
  `make all`, and `make release-check` passed with 97 pytest tests.

## Next decision gates

1. Complete WP-005: review and commit the release candidate, push it, create
   and push annotated `v0.1.0`, then run an isolated smoke test against the
   public tag and record evidence.
