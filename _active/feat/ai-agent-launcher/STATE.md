# State

## Current status

Active as of 2026-08-18. The product branch and active BC lane use
`feat/ai-agent-launcher`; the product branch is published. WP-001 is complete
and WP-002 is complete.

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

## Next decision gates

1. Plan WP-003, the versioned launcher lifecycle and explicit Codex migration.
