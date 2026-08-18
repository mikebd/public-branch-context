# Port shared Codex launcher to Python

## Decision

The shared Codex launcher has outgrown Bash. Its responsibilities now include
generated-launcher rendering and updates, Codex session JSONL discovery,
worktree lifecycle cleanup, configuration loading, collision handling, and
portable-shell compatibility. A clean Python implementation in the Python
scripts repository is the recommended direction.

## Current contract to assess before implementation

The future implementation must deliberately account for the current shared
commands and their consumers:

- Run Codex for a worktree with configured and launcher-local writable paths.
- Create new and stacked worktrees with collision rejection and cleanup.
- Pin, fork, and adopt sessions from Codex session metadata.
- Render generated launchers with worktree, session, runner, and local
  `--add-dir` metadata; preserve existing generated launchers through a
  documented migration or compatibility strategy.
- Preserve the repository-adapter boundary and the current Bash-compatible
  launcher configuration until an explicit replacement policy is approved.

## Boundaries

- This Branch Context authorizes no product, Bash, Python, adapter, generated
  launcher, configuration, or documentation changes outside this lane.
- Do not create a WBS or implementation plan until the port scope and
  compatibility strategy are explicitly approved.
- Keep user-facing documentation Branch-Context agnostic.
