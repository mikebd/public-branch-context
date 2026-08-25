# WP-009: Static shell completion

Status: complete

## Outcome

`ai-agent-launcher` emits static command completion scripts for bash, fish,
tcsh, and zsh from its existing `argparse` parser, without modifying a user's
shell configuration.

## Implementation

- Add `shtab>=1.11.0` as a runtime dependency and refresh `uv.lock`.
- Add `ai-agent-launcher completion [--shell {bash,fish,tcsh,zsh}]`. Without
  `--shell`, derive the shell name from `$SHELL`; an explicit value takes
  precedence. Missing or unsupported detection reports an actionable error
  directing the user to `--shell`.
- Generate the script from the root parser so commands, options, subcommands,
  and choices remain aligned as the CLI evolves. Do not complete adapter-owned
  passthrough arguments after `--`.
- Write the generated script only to standard output. Do not write dotfiles,
  modify `$fpath`, or install files into shell-owned directories.
- Document automatic detection, explicit shell selection, and persistent
  activation patterns for each supported shell. Recommend explicit selection
  for scripts saved to disk.

## Verification

- Add focused CLI tests for generation for each supported shell, automatic
  `$SHELL` detection, explicit override precedence, and unsupported detection.
- Assert generated scripts expose the launcher command and command hierarchy.
  Do not add interactive per-shell runtime tests; `shtab` owns shell-specific
  generated-script semantics.
- Run `make all` and `make release-check`.

## Boundaries

- `$SHELL` is the only automatic discovery signal; do not inspect process
  ancestry.
- This package emits completion code only; installation and shell-startup
  configuration remain user-owned.

## Completion evidence

- 2026-08-23: `make all` passed with Ruff, Pyright, and 96 pytest tests.
- 2026-08-23: `make release-check` passed, including source and wheel builds
  plus the isolated temporary Git-tag installation check.
- 2026-08-23: The generated zsh completion script passed `zsh -n`.
