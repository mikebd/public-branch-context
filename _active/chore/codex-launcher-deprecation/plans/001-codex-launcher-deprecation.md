# Deprecate the Bash Codex Launcher

Status: implementation complete (2026-08-26)

## Summary

Deliver this as one breaking Bash Scripts change, with no WBS: extract the only
reusable requirement checks, make the generic worktree-link engine
self-sufficient, and remove the entire `codex/launcher/` subsystem. Downstream
adapter migrations remain separate fast follows.

## Implementation changes

1. Add `lib/requirements.sh` and `lib/README.md`.
   - Expose `mikebd_require_command <command> [hint]` and `mikebd_require_git`.
   - Preserve the current missing-command status and diagnostic text exactly.
   - Keep it source-safe and within the Bash 3.2 portability contract; do not
     carry over Codex, jq, session, configuration, or launcher helpers.

2. Update `git/worktree-links.sh`.
   - Load `lib/requirements.sh` through namespaced, caller-safe initialization.
   - Call `mikebd_require_git` at the start of `mikebd_worktree_links_main`
     before Git operations.
   - Preserve the existing adapter API, link behavior, output, and argument
     handling.

3. Remove the launcher capability atomically.
   - Delete `codex/launcher/` with no compatibility shim, alias, or transition
     copy.
   - Remove launcher-only fixtures and `tests/test-launcher.sh`; trim
     `tests/test-lib.sh` so it contains only generic test support.
   - Add focused requirements tests; revise source-isolation and portability
     tests to cover `lib/requirements.sh` and `git/worktree-links.sh`; update
     the portable-DX runner accordingly.

4. Update documentation and repository guidance.
   - Replace launcher references in the root README and portability inventory
     with the generic library and worktree-link engine.
   - Document that the worktree-link engine supplies its own Git prerequisite,
     while external consumers can source `lib/requirements.sh` directly.
   - Update `AGENTS.md` to list `lib/requirements.sh` instead of the removed
     launcher directory as a portable path.

## Test plan

- Verify the generic command check for an available command, a missing command,
  and a missing command with its optional hint; verify the Git wrapper.
- Verify worktree-link behavior still works when the test sources only the
  engine, proving it loads and uses the generic requirement library itself.
- Verify source isolation and Bash 3.2 constraints for both retained portable
  scripts.
- Run `bash -n`, ShellCheck on all touched shell files,
  `tests/run-portable-dx.sh`, `git grep` confirming no tracked
  `codex/launcher` references, and `git diff --check`.

## Assumptions

- The clean `chore/codex-launcher-deprecation` branch is the intended product
  branch.
- Breaking generated launchers and external adapters is intentional; no
  consumer migration is included here.
- This remains one bounded repository delivery, so a single plan is sufficient
  and no WBS is created.
