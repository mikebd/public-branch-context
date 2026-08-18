# State

## Current status

Active as of 2026-08-18. The product branch and active BC lane now use
`feat/ai-agent-launcher`. WP-001 is complete; its product and BC changes are
ready for manual review, commit, and push.

## Evidence

- WP-001 adds the `ai-agent-launcher` console command, agent-neutral
  identifiers and session references, an immutable internal adapter registry,
  and the identity-only built-in `codex` adapter.
- Its focused coverage and the full project validation passed on 2026-08-17:
  `make all` reported Ruff and Pyright clean with 49 passing pytest tests;
  `uv run ai-agent-launcher --help` and `--version` also passed.
- Neither `chore/port-codex-launcher` nor `feat/ai-agent-launcher` exists on
  the product remote, so the branch rename is local only until this work is
  pushed.

## Next decision gates

1. Commit and push the aligned product and BC changes after manual review.
2. Select and plan WP-002 before adding Codex runtime or TOML configuration
   behavior.
