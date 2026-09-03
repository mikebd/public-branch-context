# Launcher artifact evolution and migration

## Decision

Defer cross-version generated-launcher migration design until a concrete
compatibility trigger exists. The current artifact policy remains versioned,
self-describing metadata with an inspectable optional-extension envelope; it
does not imply automatic conversion between metadata formats.

## Boundaries

- This context authorizes no product, adapter, configuration, generated
  launcher, documentation, release, or consumer-repository change.
- Do not add a migration command, conversion implementation, or cross-format
  extension rule before an activation trigger supplies real compatibility
  constraints.
- Do not create a WBS or implementation plan while this work remains
  deferred.

## Lane transitions

The lane directory is part of this BC's path, so any link that reaches it by
path breaks when it is activated. Before or with any move out of `_deferred`,
check for inbound links and update them. Nothing links here by path today:
this branch's [`README.md`](../../../README.md) does not name a lane, and the
`py-scripts-context` row in the [public Branch Context
catalog](https://github.com/mikebd/public-branch-context/blob/main/README.md)
points at `_done/feat/ai-agent-launcher` instead. Recheck rather than assume —
if this BC has since been linked from that table, the row needs the same
update.
