# Clarify persistent-launcher installation and checkout testing

Status: complete

## Summary

- Document persistent generated launchers and untagged-checkout direct CLI use
  as distinct supported modes.
- Keep the generated shim, scoped ADR 0003, and release-check implementation
  unchanged.

## Documentation

- Make tagged `uv tool install` with an available tool binary on `PATH` the
  primary persistent-launcher contract. Describe `uv tool update-shell` as an
  explicit user-owned option and manual PATH setup as equivalent.
- Reframe `uv run` as direct, one-shot CLI use from an untagged checkout. It
  does not install the command for later direct execution by a generated
  launcher.
- Reframe the isolated local `uv tool install --no-cache .` recipe as the
  supported no-tag installation test. Export its temporary binary directory to
  `PATH` so a generated-launcher smoke test in that temporary session resolves
  the isolated tool.
- State that `make release-check` validates the temporary Git-tagged
  installation plus `--version` and `--help`, not generated-launcher
  execution.

## Boundaries

- Do not add checkout-wrapper modes, change the shell shim, change runtime
  behavior, revise ADR 0003, or expand release-check coverage.
- Do not claim automated generated-launcher lifecycle coverage.

## Verification

- Verify rewritten documentation links and command examples.
- Run `git diff --check`, `make all`, and `make release-check`.

## Assumptions

- The temporary installation works only while its directory and PATH entry are
  retained.
- Existing shim PATH-delegation coverage is sufficient for this
  documentation-only refinement.

## Completion evidence

- 2026-08-24: Verified the rewritten documentation examples and link targets.
- 2026-08-24: `git diff --check`, `make all` (97 pytest tests), and
  `make release-check` (temporary Git-tagged installation) passed.
