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
