# State

## Current status

Deferred as of 2026-08-25. No released incompatible launcher metadata format
or artifact-migration need currently exists.

## Activation triggers

Resume this context when any of the following is true:

1. A proposed launcher metadata change cannot safely remain in the current
   format.
2. A release would retire support for an earlier metadata reader.
3. A real launcher artifact cannot reasonably be recreated and needs an
   explicit transition path.

## Decisions to make when activated

- Define the supported-format compatibility window from actual released
  artifacts.
- Choose explicit recreation, user-invoked migration, or continued reader
  compatibility.
- Specify the treatment of each persisted extension for the selected format
  transition.
- If migration is required, define confirmation, atomicity, rollback,
  idempotency, diagnostics, documentation, and compatibility tests.
