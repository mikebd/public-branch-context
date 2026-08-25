# WBS: AI agent launcher port

## Delivery decisions

- The product is named `ai-agent-launcher`; the initial supported adapter is
  `codex`.
- The generic core owns Git worktree lifecycle, generated-launcher lifecycle,
  configuration loading, and safety checks. An internal typed adapter registry
  owns agent CLI arguments, writable-directory mechanics, and session
  semantics.
- The first release is distributed as a versioned Git tag through `uv tool
  install`, not PyPI. Legacy Codex configuration and launcher migration is not
  part of the released interface.
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
- Status: complete.
- Plans: [003-versioned-launcher-lifecycle-and-codex-migration.md](plans/003-versioned-launcher-lifecycle-and-codex-migration.md).
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
- Status: complete.
- Plans: [004-neutral-worktree-creation-and-stacking.md](plans/004-neutral-worktree-creation-and-stacking.md).
- Scope: Python scripts repository only; no Bash or consumer adapter edits.

### WP-005 — Parity verification, tagged distribution, and public guidance

- Outcome: Automated contract coverage and public documentation make a tagged
  `ai-agent-launcher` release reproducible from this repository or a fork,
  without shipping legacy migration behavior.
- Acceptance: The three current local launchers are manually recreated and
  privately verified before legacy migration code is removed. Tests use
  temporary Git repositories, fake Codex executables, synthetic session JSONL,
  and a fake adapter to prove core/adapter separation and legacy runtime
  parity. Validation covers pytest, Ruff, Pyright, package build, and a clean
  tagged `uv tool install` smoke test. Documentation shows upstream and fork
  Git-tag installation, tag/version creation, PATH setup, selected-tag
  upgrades, and the absence of PyPI publication.
- Parent: none.
- Depends on: WP-001, WP-002, WP-003, WP-004.
- Status: in progress.
- Plans: [005-release-hardening-and-v0.1.0-tag.md](plans/005-release-hardening-and-v0.1.0-tag.md).
- Scope: Python scripts repository only; user-facing documentation remains
  Branch-Context agnostic.

### WP-006 — Buds migration BC handoff

- Outcome: A receiving Branch Context in the Buds `_todo` lane frames its
  future launcher policy and consumer documentation from the released Python
  core.
- Acceptance: After resolving the receiving BC root, create only customary
  `CONTEXT.md` and `STATE.md` for the handoff lane. Record that the current
  local launcher replacements were completed during WP-005; do not create a
  product branch or edit Buds files in this package.
- Parent: none.
- Depends on: WP-005.
- Status: planned.
- Plans: none.
- Scope: Handoff artifact only; the receiving BC owns all Buds implementation.

### WP-007 — Sharpen Blade migration BC handoff

- Outcome: A receiving Branch Context in the sharpen-blade `_todo` lane frames
  its future launcher policy and consumer documentation from the released
  Python core.
- Acceptance: After resolving the receiving BC root, create only customary
  `CONTEXT.md` and `STATE.md` for the handoff lane. Record that the current
  local launcher replacement was completed during WP-005; do not create a
  product branch or edit sharpen-blade files in this package.
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

### WP-009 — Static shell completion

- Outcome: `ai-agent-launcher` emits static shell completion from its existing
  `argparse` interface for bash, fish, tcsh, and zsh.
- Acceptance: `completion` accepts an explicit supported shell or derives one
  from `$SHELL`; generated scripts expose the current CLI hierarchy. The
  command writes only to stdout, and public documentation explains
  user-managed activation without modifying shell configuration.
- Parent: none.
- Depends on: WP-001.
- Status: complete.
- Plans: [006-static-shell-completion.md](plans/006-static-shell-completion.md).
- Scope: Python scripts repository only; no shell configuration, adapter, or
  consumer repository changes.

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
- 2026-08-18: Completed WP-003. Versioned POSIX-shell launchers, neutral
  lifecycle orchestration, the Codex session adapter, and explicit legacy
  migration are covered by synthetic Git, executable, and JSONL tests.
  `make all` passed (73 tests), and the installed CLI exposed the launcher and
  migration command groups.
- 2026-08-19: Started WP-004 and captured its implementation plan at
  `plans/004-neutral-worktree-creation-and-stacking.md`.
- 2026-08-21: Completed WP-004. Neutral worktree creation and strict stacking
  retain the accepted branch, start-ref, collision, rollback, unpinned
  launcher, and source-HEAD behavior. `make all` passed with 83 tests.
- 2026-08-22: Started WP-005 and captured its approved release-hardening plan.
  WP-005 now owns one-time private replacement of the three current local
  launchers before removal of the released migration surface. WP-006 and
  WP-007 remain planned, narrowed to consumer policy and documentation.
- 2026-08-22: Completed WP-005's local cutover and release-candidate
  validation. The three local legacy launchers were privately backed up and
  recreated; two retained pinned sessions and one remained unpinned. `make
  release-check` passed with 82 tests, a source build, and isolated temporary
  Git-tag installation. Public tag publication remains the final WP-005 gate.
- 2026-08-23: Started WP-009 and captured its approved completion plan. It
  adds static parser-derived completion for bash, fish, tcsh, and zsh without
  writing shell configuration.
- 2026-08-23: Completed WP-009. `make all` and `make release-check` passed
  with 96 pytest tests, and the generated zsh script passed `zsh -n`.
- 2026-08-18: Completed WP-002. `make all` passed (Ruff, Pyright, and 66
  pytest tests); the installed command's help and version smoke tests passed.
- 2026-08-18: Started WP-003 and captured its approved implementation plan at
  `plans/003-versioned-launcher-lifecycle-and-codex-migration.md`.
