# feat/claude — Claude Code as a first-class agent

Lane: `_done`. Placed here directly on completion so the PR and later
references have a stable path.

## Goal

RMAR and BC supported Codex only. Extend both to Claude Code before publishing
the repo to a new team. The team is on macOS; the author is the only Linux
user, which shapes most of the risk below.

## Durable decisions

Recorded because the reasoning is not recoverable from the diff.

**Agent-neutral contract, per-agent overlays.** `DEVELOPER_INSTRUCTIONS.md`
defines constrained execution, elevated execution, scoped approval and
`<agent-home>`; each agent's `EXECUTION_MODEL.md` maps them onto real
mechanics. Adding a third agent means adding an overlay, not editing the
contract. `DOC_MAP.md` states this rule so it survives.

**`git commit` and `git push` stay out of `claude/settings.json.example`.**
Not an oversight. "Commit/Push Controls" requires a hold for manual review; in
Claude Code the permission prompt *is* that hold, so allowlisting either would
delete the control the rule exists to enforce.

**RMAR must not name, import or index BC.** An `ACRONYMS.md` entry for `BC` was
added and reverted for this. Note the rule as written in the BC README forbids
*importing or indexing*; a boundary statement is not a violation, which is why
"do not belong in repo docs or branch context" in `DEVELOPER_INSTRUCTIONS.md`
was kept when a review proposed replacing it. The proposed replacement
("transient task context") also inverted BC's defining property — BC is
durable.

**The baseline allowlist pre-authorizes no package installs.** Added
2026-09-03, from a CodeRabbit finding on PR #1. `DEVELOPER_INSTRUCTIONS.md`
names `npm install`, `npm ci`, `uv sync`, `uv pip install`, `pip install` and
`poetry install` as prefixes to scope-approve, and `settings.json.example`
originally carried all of them. It no longer does. The reading that resolves the
apparent conflict: the contract names the *correct prefixes to use when you
pre-authorize*, and never requires pre-authorizing them at all — so declining to
is compliance, not deviation. The hazard is concrete (`npm install` runs
lifecycle scripts; `pip install` and `uv pip install` take an agent-chosen
source), the cost of the prompt is one approval per session, and installs are
rarely in the inner loop. They move to a labelled opt-in section in
`claude/EXECUTION_MODEL.md` with the hazard stated, since JSON has no comment
syntax to hold the warning in the example file itself. `uv lock` stays in the
baseline by explicit decision: it resolves rather than installs.

This is the same shape as the `git commit` / `git push` decision above — the
example file is not obliged to pre-authorize everything the contract knows how
to scope.

**Absolute paths in bootstrap files.** Tilde expansion depends on the reader
performing it and is not guaranteed across surfaces.

**Verification records belong in BC, not in the reference docs.** The product
docs say *how* to verify; BC records *that we did*. Two dated verification
blocks were written into `claude/EXECUTION_MODEL.md` and the agent-runtime
README, reviewed, and reverted for this reason. The distinction that matters:
a surprising or negative finding earns a place in the docs (see the ChatGPT
desktop table in `codex/EXECUTION_MODEL.md`); a confirmation that documented
behaviour works does not.

## Standing constraint

Nothing in this branch has executed on macOS. Both defects found late were
found by running things for real, not by reading them — see `STATE.md`.
