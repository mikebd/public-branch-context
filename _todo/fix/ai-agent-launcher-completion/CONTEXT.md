# Agent-specific shell completion follow-up

## Goal

Decide whether `ai-agent-launcher` should replace its agent-specific static
completion setup with a single completion experience that correctly exposes
`run` options for both Claude and Codex.

## Current state

The release candidate has two adapters with conflicting runtime options. Its
static completion command accepts `--agent claude|codex`, generating a script
for one adapter at a time. The default remains the first registered adapter.
Users who use both adapters must select and install one adapter-specific
script.

## Constraints

- Preserve user-owned completion installation; do not edit shell startup files
  or privileged completion directories.
- The accepted repository ADR requires static, parser-derived completion by
  default. A dynamic alternative requires an explicit, documented exception or
  superseding decision.
- Keep completion aligned with the public parser contract and avoid misleading
  suggestions for the selected adapter.

## Decision to make

Compare an adapter-aware dynamic completion design with the current
adapter-specific static scripts (and any static parser-derived alternative).
Choose the model that gives users of both adapters correct completion with an
acceptable runtime, security, maintenance, and shell-compatibility cost.
