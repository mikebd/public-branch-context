# WP-003: Versioned launcher lifecycle and explicit Codex migration

Status: complete

## Outcome

Add versioned POSIX-shell launchers for existing Git worktrees, generic
launcher lifecycle commands, and explicit Codex-only migration from legacy
Bash configuration and generated launchers. Worktree creation and stacking
remain deferred to WP-004.

## Approved interface

```text
ai-agent-launcher launcher create --agent ID --launcher PATH --worktree-dir PATH --marker TEXT
  [--prepare PATH] [--add-dir PATH]...
ai-agent-launcher launcher pin --launcher PATH --session-id ID [--agent ID] [--replace]
ai-agent-launcher launcher fork --launcher PATH --target-launcher PATH [--agent ID]
  [--add-dir PATH]... [--] [AGENT_ARGUMENT...]
ai-agent-launcher launcher adopt --launcher PATH --target-launcher PATH --session-id ID
  [--agent ID] [--add-dir PATH]...
ai-agent-launcher migrate config --from PATH [--to PATH]
  --trust-legacy-shell-config [--replace]
ai-agent-launcher migrate launcher --agent codex --from PATH --to PATH
  --marker TEXT --prepare PATH [--replace]
```

Generated launchers are mode-0700 `/bin/sh` shims. They retain the exact
single-line marker and a versioned URL-safe-base64 JSON metadata comment, then
delegate execution through `ai-agent-launcher launcher run --launcher "$0"`.
The metadata holds the agent, opaque optional session ID, canonical worktree,
optional absolute preparation executable, and ordered canonical local
writable directories.

## Behavior

- Pin, fork, and adopt derive the adapter from source metadata; optional
  `--agent` values are assertions. A changed pin or occupied migration target
  requires `--replace`.
- The preparation executable runs as `PATH --target WORKTREE` before launches.
  It is optional when creating a launcher and mandatory for legacy launcher
  migration because consumer policy cannot be inferred.
- Codex fork snapshots session metadata, launches the child with the parent
  session and any post-`--` arguments, and renders a target only for exactly
  one new matching child. Adopt requires exactly one matching-worktree session;
  parent mismatch is informational.
- Config migration sources a legacy fragment only with the explicit trust flag,
  maps known launcher settings to TOML, and warns for unsupported variables.
  Launcher migration requires the exact expected marker and reads only the
  supported legacy generated metadata.

## Verification

Use temporary Git worktrees, fake Codex executables, synthetic session JSONL,
and synthetic legacy inputs to cover rendering, POSIX execution, lifecycle
safety, migration, atomic replacement, marker/mode preservation, and ordered
directory behavior. Run focused tests, `make all`, CLI help, and a generated
shim smoke test.

Completed 2026-08-18: `make all` passed (Ruff, Pyright, 73 pytest tests). The
focused lifecycle coverage includes a POSIX-shim smoke test, preparation and
fork lifecycle behavior, synthetic session metadata, and trusted legacy
migration fixtures.

## Exclusions

No Git worktree creation or stacking, Bash or consumer-repository edits,
runtime fallback for legacy shims, public documentation, or non-Codex adapter
implementation.
