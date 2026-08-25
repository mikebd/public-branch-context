# Generic ADR framing

Status: complete

## Outcome

The repository ADR convention and its first record state durable rules
generically, without using the first implementation, Branch Context, WBS, or
plan artifacts as their framing.

## Implementation

- Add explicit generic-framing rules to the ADR lifecycle and authoring
  guidance. Product-specific references are exceptional: they must be
  material to the decision or clearly labeled examples that generic wording
  cannot replace.
- Rewrite the uncommitted first ADR directly so its initial accepted baseline
  describes static parser-derived shell completion for a generic CLI rather
  than a particular product feature.
- Keep the root ADR activation guidance generic and verify the README links
  and index remain consistent.

## Boundaries

- This plan is unassociated with a WBS work package and does not change
  WP-009.
- Do not change implementation behavior or shell-completion user guidance.

## Verification

- Review ADR documents for prohibited implementation-specific framing and
  confirm any remaining named technology is material to the decision.
- Run `git diff --check` and `make all`.

## Completion evidence

- 2026-08-23: Added explicit generic-framing rules to the ADR index and
  authoring guide, then rewrote the uncommitted first ADR as a generic CLI
  standard.
- 2026-08-23: The first ADR contains no `ai-agent-launcher`, Branch Context,
  WBS, plan, or agent-pass-through reference. `git diff --check` and `make
  all` passed with Ruff, Pyright, and 96 pytest tests.
