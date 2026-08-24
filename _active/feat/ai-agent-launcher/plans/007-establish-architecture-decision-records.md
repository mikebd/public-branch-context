# Establish architecture decision records

Status: complete

## Outcome

The repository has an ADR convention for durable decisions, and its first
accepted record establishes static parser-derived shell completion as the
standard for supported command-line tools.

## Implementation

- Add an indexed `docs/adr/` lifecycle and authoring guide modeled on the
  Buds ADR convention, including immutable accepted baselines, dated
  clarifying amendments, and superseding records for material changes.
- Add accepted ADR `0001` for static Shtab-generated completion from the
  authoritative `argparse` parser. It covers the `completion --shell` public
  contract, `$SHELL` detection, bash/fish/tcsh/zsh support, stdout-only
  emission, and user-owned installation.
- Activate nested `AGENTS.md` discovery and immediate rereading of changed
  guidance in the root agent instructions. Add ADR-specific decision triggers.
- Link the root README to the ADR index and explain its purpose.

## Boundaries

- This plan is intentionally unassociated with a WBS work package. It does
  not revise the existing WP-009 record.
- Do not change completion behavior, generated completion code, or shell
  installation behavior as part of this documentation work.

## Verification

- Review ADR links, numbering, index, lifecycle, and activation instructions.
- Run `git diff --check` and `make all`.

## Completion evidence

- 2026-08-23: Added the ADR index, authoring guide, accepted static-completion
  record, root activation guidance, and README entry.
- 2026-08-23: `git diff --check` and `make all` passed with Ruff, Pyright,
  and 96 pytest tests.
