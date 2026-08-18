# WBS: AI agent launcher port

## Delivery decisions

- The product is named `ai-agent-launcher`; the initial supported adapter is
  `codex`.
- The generic core owns Git worktree lifecycle, generated-launcher lifecycle,
  configuration loading, and safety checks. An internal typed adapter registry
  owns agent CLI arguments, writable-directory mechanics, and session
  semantics.
- The first release is distributed as a versioned Git tag through `uv tool
  install`, not PyPI. It migrates legacy Codex configuration and launchers
  only through explicit user commands.
- This WBS is flat. Its first delivery creates this file only; it authorizes
  no product, adapter, documentation, or follow-up BC changes by itself.

## Work packages

### WP-001 — Neutral launcher core and adapter boundary

- Outcome: A strictly typed `ai_agent_launcher` package and
  `ai-agent-launcher` console command define generic worktree, launcher, and
  session-reference models plus an internal `AgentAdapter` registry.
- Acceptance: The registry contains only the built-in `codex` adapter; core
  types and commands do not depend on Codex environment variables, JSONL
  layout, or command-line flags. No third-party plugin discovery or other
  agent integration is introduced.
- Parent: none.
- Depends on: none.
- Status: complete.
- Plans: [001-neutral-launcher-core-and-adapter-boundary.md](plans/001-neutral-launcher-core-and-adapter-boundary.md).
- Scope: Python scripts repository only.

### WP-002 — Codex runtime and TOML configuration adapter

- Outcome: `CodexAdapter` translates neutral launch requests into Codex
  invocations and owns Codex-specific environment, writable-directory, and
  session-discovery behavior. Configuration moves to
  `$XDG_CONFIG_HOME/ai-agent-launcher/config.toml`, with generic values under
  `[core]` and Codex values under `[agents.codex]`.
- Acceptance: `run --agent codex` preserves supported Codex resume, fork,
  sandbox, model, reasoning, cache, and writable-directory behavior without
  sourcing a Bash configuration fragment during normal execution.
- Parent: none.
- Depends on: WP-001.
- Status: complete.
- Plans: [002-codex-runtime-and-toml-configuration.md](plans/002-codex-runtime-and-toml-configuration.md).
- Scope: Python scripts repository only; Python 3.13+ and Linux remain the
  supported baseline.

### WP-003 — Versioned launcher lifecycle and explicit Codex migration

- Outcome: Versioned generated launchers store the selected `agent_id`, opaque
  session reference, worktree, marker, preparation path, and ordered local
  writable directories. They are minimal POSIX-shell shims that call
  `ai-agent-launcher` from `PATH`.
- Acceptance: Pin, fork, and adopt derive the adapter from the source launcher
  and reject conflicting agent selection. Explicit `migrate config` and
  `migrate launcher --agent codex` safely import approved legacy Codex data,
  preserve marker and executable mode, and replace files atomically. Migration
  requires an explicit trusted legacy shell-config opt-in and an
  adapter-owned preparation path; it never guesses consumer policy.
- Parent: none.
- Depends on: WP-001, WP-002.
- Status: planned.
- Plans: none.
- Scope: Python scripts repository only; existing consumer launchers are not
  migrated by this package.

### WP-004 — Git worktree and session workflow parity

- Outcome: Neutral worktree creation and stacking delegate rendering and
  runtime semantics to the selected adapter, while the Codex adapter supports
  the current lifecycle behavior.
- Acceptance: `worktree new --agent codex` and `worktree stack --agent codex`
  retain collision rejection, owned-resource rollback, explicit start-ref
  behavior, committed-HEAD stacking, and unpinned launcher creation. Codex
  pin, fork, and adopt retain exact-one-session, matching-worktree, ordered
  add-directory, and parent-mismatch behavior.
- Parent: none.
- Depends on: WP-002, WP-003.
- Status: planned.
- Plans: none.
- Scope: Python scripts repository only; no Bash or consumer adapter edits.

### WP-005 — Parity verification, tagged distribution, and public guidance

- Outcome: Automated contract coverage and public documentation make a tagged
  `ai-agent-launcher` release reproducible from this repository or a fork.
- Acceptance: Tests use temporary Git repositories, fake Codex executables,
  synthetic session JSONL, and a fake adapter to prove core/adapter separation
  and legacy behavior parity. Validation covers pytest, Ruff, Pyright, package
  build, and a clean tagged `uv tool install` smoke test. Documentation shows
  upstream and fork Git-tag installation, tag/version creation, PATH setup,
  selected-tag upgrades, and the absence of PyPI publication.
- Parent: none.
- Depends on: WP-001, WP-002, WP-003, WP-004.
- Status: planned.
- Plans: none.
- Scope: Python scripts repository only; user-facing documentation remains
  Branch-Context agnostic.

### WP-006 — Buds migration BC handoff

- Outcome: A receiving Branch Context in the Buds `_todo` lane frames its
  future adapter, configuration, generated-launcher, and documentation
  migration from the released Python core.
- Acceptance: After resolving the receiving BC root, create only customary
  `CONTEXT.md` and `STATE.md` for the migration lane. Do not create a product
  branch, edit Buds files, or migrate user launchers in this package.
- Parent: none.
- Depends on: WP-005.
- Status: planned.
- Plans: none.
- Scope: Handoff artifact only; the receiving BC owns all Buds implementation.

### WP-007 — Sharpen Blade migration BC handoff

- Outcome: A receiving Branch Context in the sharpen-blade `_todo` lane frames
  its future adapter, configuration, generated-launcher, and documentation
  migration from the released Python core.
- Acceptance: After resolving the receiving BC root, create only customary
  `CONTEXT.md` and `STATE.md` for the migration lane. Do not create a product
  branch, edit sharpen-blade files, or migrate user launchers in this package.
- Parent: none.
- Depends on: WP-005.
- Status: planned.
- Plans: none.
- Scope: Handoff artifact only; the receiving BC owns all sharpen-blade
  implementation.

### WP-008 — Bash launcher retirement BC handoff

- Outcome: A receiving Branch Context in the Bash scripts `_todo` lane defines
  retirement of the legacy shared Bash launcher after both consumer migrations
  have completed.
- Acceptance: Create only the receiving `CONTEXT.md` and `STATE.md` after
  completion evidence from the Buds and sharpen-blade migration BCs. The
  receiving BC decides removal sequencing and performs no retirement as part
  of this WBS package.
- Parent: none.
- Depends on: completion evidence from the migration BCs created by WP-006 and
  WP-007.
- Status: blocked.
- Plans: none.
- Scope: Handoff artifact only; no Bash-script removal or compatibility change
  occurs here.

## History

- 2026-08-17: Created as the approved WBS-first delivery map. It records the
  neutral AI-agent orchestration boundary, the Codex-only first adapter, and
  planning-only handoffs for downstream migrations and Bash retirement.
- 2026-08-17: Started WP-001 and captured its implementation plan at
  `plans/001-neutral-launcher-core-and-adapter-boundary.md`.
- 2026-08-17: Completed WP-001. `make all` passed (Ruff, Pyright, and 49
  pytest tests); `uv run ai-agent-launcher --help` and `--version` passed,
  with the installed command reporting `0.1.0`.
- 2026-08-18: Started WP-002 and captured its implementation plan at
  `plans/002-codex-runtime-and-toml-configuration.md`.
- 2026-08-18: Completed WP-002. `make all` passed (Ruff, Pyright, and 66
  pytest tests); the installed command's help and version smoke tests passed.
