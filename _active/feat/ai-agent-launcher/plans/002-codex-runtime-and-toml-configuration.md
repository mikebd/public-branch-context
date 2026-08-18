# WP-002: Codex runtime and TOML configuration adapter

Status: complete

## Outcome

Add `ai-agent-launcher run --agent codex` with a neutral dispatch boundary and
a Codex-owned TOML/runtime implementation. Worktree creation, generated
launchers, migration, and session lifecycle commands remain deferred.

## Interface

```text
ai-agent-launcher [--config PATH] run --agent codex
  [--worktree-dir PATH]
  [--add-dir PATH]...
  [--session-id ID | --fork-session-id ID]
  [--model MODEL]
  [--reasoning-effort VALUE]
  [--sandbox read-only|workspace-write|danger-full-access]
  [--] [CODEX_ARGUMENT...]
```

Configuration defaults to `$XDG_CONFIG_HOME/ai-agent-launcher/config.toml`;
`--config` overrides that location. It contains generic `core.writable_dirs`
and Codex-specific settings under `[agents.codex]`. Explicit writable roots
must be absolute, existing directories. `CODEX_HOME` overrides configured
Codex home; model, reasoning effort, and sandbox can be overridden per run.

## Implementation

- Keep generic worktree validation and adapter dispatch in the core; give the
  Codex adapter its own parser options, config validation, environment,
  writable-root resolution, command rendering, and session catalog.
- Require an existing Git worktree and never create one implicitly. Preserve
  configured then CLI writable-root ordering, canonical first-occurrence
  de-duplication, optional `.context`, Git directory, and Go/Golangci cache
  handling.
- Invoke Codex, `codex resume`, or `codex fork`; session values remain opaque
  and mutually exclusive. Pass arguments after `--` through unchanged.
- Read session metadata from the selected Codex home without exposing session
  lifecycle commands yet.

## Verification

- Cover TOML defaults, explicit selection, validation, precedence, command
  rendering, runtime environment, writable roots, worktree errors, and fake
  Codex exit propagation.
- Cover synthetic session JSONL discovery, malformed records, and duplicate
  IDs.
- Run focused tests, `make all`, and a fake-executable command smoke test.

Completed 2026-08-18: focused adapter tests (35), `make all` (66 tests), and
the installed command's help/version smoke tests passed. The fake-executable
runtime tests validate command rendering, environment precedence, and exit
status propagation.

## Deliberate exclusions

No Bash configuration sourcing, worktree creation, generated launcher
rendering, migration, pin/fork/adopt commands, public documentation, or
non-Codex adapters are included.
