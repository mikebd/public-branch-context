# Establish tool-scoped decision records for ai-agent-launcher

Status: complete

## Summary

- Keep `docs/adr/` exclusively for repository-wide policy.
- Establish `docs/ai-agent-launcher/adr/` for durable decisions scoped to this
  tool.
- Use a small nested source `AGENTS.md` to activate the scoped guidance for
  launcher implementation work; do not use it as the decision-history store.

## Governance and activation

- Add a scoped ADR README and authoring guide that inherit the root ADR
  lifecycle, immutable-baseline and amendment model, record structure,
  generic-framing rules, and reciprocal related-record convention.
- Allocate scoped numbers independently after scanning the scoped directory in
  every non-bare worktree. Do not add scoped records to the root ADR index.
- Update root guidance to route repository-wide decisions to `docs/adr/` and
  tool-specific durable decisions to the applicable scoped ADR area.
- Add `src/ai_agent_launcher/AGENTS.md` directing agents to read the scoped
  guide before changing a durable launcher contract, adapter boundary,
  configuration model, or persisted launcher format.
- Link the scoped decision index from the public launcher guide.

## Initial accepted records

- Use internal capability-based adapters for agent integrations.
- Use XDG TOML configuration with core and agent namespaces.
- Use versioned self-describing generated launcher artifacts.

Each record must use generic language within the tool's scope and must not
refer to Branch Context, WBS, plans, or the first adapter or implementation.

## Validation

- Verify scoped numbering across all worktrees, index links, root-versus-scoped
  routing, and nested-guidance activation.
- Run `git diff --check` and `make all`.
- Do not change launcher behavior, release policy, or repository-wide ADR
  decisions.

## Assumptions

- The three implemented choices are established behavior, so their scoped
  records begin as **Accepted**.
- A future decision affecting multiple tools is promoted to the root ADR area
  and cross-linked reciprocally.

## Completion evidence

- 2026-08-24: Scanned the single non-bare worktree before allocating scoped
  records `0001` through `0003`; no scoped record numbers were previously
  claimed.
- 2026-08-24: Verified local documentation targets, `git diff --check`, and
  `make all` (Ruff clean, Pyright clean, 97 pytest tests passed).
