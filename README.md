# Public Branch Context

This repository is a public catalog of Branch Context (BC) branches: optional,
branch-scoped working context that helps coding-agent work remain resumable,
traceable, reviewable, and easier to hand off.

The `main` branch is the orientation and discovery entrypoint. The other
branches preserve public BC for particular repositories; they are context
storage branches, not replacements for the product repositories themselves.

## Start here

- [Branch Context adoption guide](https://github.com/mikebd/ai-agent-skills/blob/main/shared/references/branch-context/README.md)
- [Canonical Branch Context reference](https://github.com/mikebd/ai-agent-skills/blob/main/shared/references/branch-context/BRANCH_CONTEXT.md)
- [ai-agent-skills repository](https://github.com/mikebd/ai-agent-skills)

## What a Branch Context can contain

A BC is shaped by the work, so not every BC contains every artifact. Depending
on the workflow, readers may find:

- `CONTEXT.md` for durable intent, constraints, assumptions, and decisions.
- `STATE.md` for current progress, findings, evidence, and handoff state.
- An optional `WBS.md` for deliverable-oriented decomposition and dependencies.
- Approved implementation plans under a `plans/` directory.
- Reusable methods, scripts, artifacts, audit outputs, and navigation links.
- Lane and lifecycle information showing whether work is active, complete,
  deferred, under review, or otherwise paused.

BC gives reviewers additional context for understanding intent, decisions, and
evidence around a change. It remains supplemental: the product repository,
tests, and pull request must remain understandable and reviewable on their
own.

## Public BC branches

The catalog below is curated to show different useful shapes of BC practice.
Each branch README is the best starting point for that branch, followed by the
representative BC path when more detail is useful.

| Public BC branch | Associated repository | Practice example |
| --- | --- | --- |
| [`ai-agent-skills-context`](https://github.com/mikebd/public-branch-context/tree/ai-agent-skills-context) | [`ai-agent-skills`](https://github.com/mikebd/ai-agent-skills) | [Completed delivery context](https://github.com/mikebd/public-branch-context/tree/ai-agent-skills-context/_done/feat/claude) paired with the [planned follow-up](https://github.com/mikebd/public-branch-context/tree/ai-agent-skills-context/_todo/fix/sync-rm-guard) it deliberately scoped out, showing reciprocal links across lanes and work recorded before its branch exists. |
| [`bash-scripts-context`](https://github.com/mikebd/public-branch-context/tree/bash-scripts-context) | [`bash-scripts`](https://github.com/mikebd/bash-scripts) | [Completed delivery context](https://github.com/mikebd/public-branch-context/tree/bash-scripts-context/_done/chore/codex-launcher-deprecation), showing decisions, state, an approved plan, and handoff evidence. |
| [`go-sharpen-blade-context`](https://github.com/mikebd/public-branch-context/tree/go-sharpen-blade-context) | [`go-sharpen-blade`](https://github.com/mikebd/go-sharpen-blade) | [Active workflow context](https://github.com/mikebd/public-branch-context/tree/go-sharpen-blade-context/_active/interview/dsa), showing ongoing state and sequential plans. |
| [`py-scripts-context`](https://github.com/mikebd/public-branch-context/tree/py-scripts-context) | [`py-scripts`](https://github.com/mikebd/py-scripts) | [WBS-oriented delivery context](https://github.com/mikebd/public-branch-context/tree/py-scripts-context/_done/feat/ai-agent-launcher), showing package decomposition, dependencies, plan links, and lane lifecycle. |

This list is intentionally curated rather than exhaustive. The public
repository and each branch remain the source of truth for additional context.

### Link stability

The branch links above are stable. The practice-example links are not, and the
reason is structural: a lane directory is part of a BC's path, and a lane is
workflow state. A BC in `_active`, `_todo`, `_hold`, `_deferred`, `_research`,
or `_idea` moves when its work does, so a deep link into one of those lanes
stops resolving as soon as the BC is promoted, paused, completed, or rejected.
Links into `_done` and `_rejected` are stable in practice because those lanes
hold terminal outcomes, and links into the persistent `__`-prefixed lanes are
stable by design.

Each catalogued BC that currently sits in a workflow-state lane carries a
follow-up item to update this table when its lane changes, so a transition
should normally reach here with the link. If a practice-example link does 404,
follow the branch link in the same row and browse its lane directories: the BC
has moved, not disappeared.

## Reading a public BC

For orientation, read the branch README first. Then read the relevant
`CONTEXT.md` and `STATE.md`, followed by any linked WBS, plans, evidence, or
artifacts needed for the question at hand. The [adoption guide](https://github.com/mikebd/ai-agent-skills/blob/main/shared/references/branch-context/README.md)
explains how to adopt the practice independently.
