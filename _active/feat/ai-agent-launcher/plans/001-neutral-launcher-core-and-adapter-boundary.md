# WP-001: Neutral launcher core and adapter boundary

Status: complete

## Outcome

Create the `ai-agent-launcher` executable and its internal, agent-neutral
foundation. The only registered adapter is an identity-only Codex adapter.
This work package deliberately excludes runtime, session, configuration, and
worktree operations.

## Implementation

- Add the `ai-agent-launcher = "ai_agent_launcher.cli:main"` console script.
- Add internal types for validated agent identifiers and opaque
  agent-associated session references.
- Add a minimal `AgentAdapter` identity protocol and immutable
  `AgentRegistry` that rejects invalid and duplicate identifiers and reports
  unknown identifiers with the available choices.
- Register an identity-only `CodexAdapter` under `codex`. It must not contain
  Codex command arguments, environment variables, JSONL handling, or session
  grammar.
- Provide only a root CLI with `--help`, `--version`, and no-argument help.
  Version reporting comes from installed distribution metadata.

## Verification

- Unit-test the value types, registry behavior, an independent non-Codex
  adapter double, and root CLI behavior.
- Run the focused tests and project checks.
- Smoke-test the installed `ai-agent-launcher` command with `--help` and
  `--version`.

## Deliberate exclusions

No dynamic plugin discovery, external SPI compatibility commitment, TOML
configuration, worktree management, generated launchers, session persistence,
or other agent adapters are included in this work package.

## Completion evidence

- 2026-08-17: `make all` passed: Ruff, Pyright, and 49 pytest tests.
- 2026-08-17: `uv run ai-agent-launcher --help` and `--version` passed; the
  installed command reported version `0.1.0`.
