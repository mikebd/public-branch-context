# WP-004: Neutral Git worktree creation and stacking

Status: complete

## Outcome

Add generic `worktree new` and `worktree stack` commands that create a Git
worktree, branch, and unpinned versioned launcher. Keep Codex-specific
runtime/session behavior in the existing adapter; do not modify Bash launchers
or consumer repositories.

## Approved interface

```text
ai-agent-launcher [--config PATH] worktree new --agent ID --worktree-dir PATH
  --marker TEXT [--branch NAME] [--from REF] [--launcher PATH]
  [--prepare PATH] [--add-dir PATH]...

ai-agent-launcher [--config PATH] worktree stack --agent ID --suffix TEXT
  --marker TEXT [--prepare PATH] [--add-dir PATH]...
```

`[core] launcher_directory` defaults to `$HOME/.local/bin`; it must be an
absolute directory path and may be created when rendering a launcher. Default
names are always `<agent>-<worktree-name>`, for example `codex-feature-x`,
with no configurable extra prefix. `worktree new` may override the exact
launcher path; strict `worktree stack` derives all targets from the attached
source worktree plus its suffix.

## Behavior

- `new` defaults the branch to the target basename and defaults `--from` to
  the primary worktree's committed `HEAD`; explicit refs are resolved before
  mutation.
- `stack` requires a safe single suffix, uses the current attached worktree's
  committed `HEAD`, and excludes uncommitted source changes.
- Both commands preflight all inputs and occupied worktree, branch, and
  launcher targets before `git worktree add`, create an unpinned launcher, and
  preserve preparation and ordered local-directory behavior from WP-003.
- On preparation or rendering failure, remove only the worktree and branch
  created by this invocation. Preserve collisions and any launcher an external
  preparation command created.

## Verification

Use temporary real Git repositories/worktrees for explicit and default new,
strict stack derivation, collision prevention, failures and rollback,
preparation, generated metadata, and CLI errors/help. Run `make all` and
command smoke tests.

Completed 2026-08-21: `make all` passed (Ruff, Pyright, 83 pytest tests). The
focused tests cover default-primary and explicit start refs, committed-HEAD
stacking, strict stack target derivation, occupied regular and symlink targets,
rollback, preparation, and passthrough-safe legacy suffix parsing.

## Exclusions

No extra agent adapter, public documentation, distribution work, consumer
migration, Bash edits, or retirement work.
