# State

## Current status

Active as of 2026-08-18. The product branch and active BC lane use
`feat/ai-agent-launcher`; the product branch is published. WP-001, WP-002,
WP-003, and WP-004 are complete.

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

## Next decision gates

1. Plan WP-005 when parity verification, tagged distribution, and public
   guidance are authorized.
