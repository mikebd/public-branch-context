# State

## Status

Todo

## 2026-09-14

- Created at the user's request after the v0.1.4 review identified that a
  single generated completion script cannot describe both adapters' conflicting
  `run` options.
- The current candidate adds `ai-agent-launcher completion --agent NAME` and
  documents the required adapter-specific setup.
- Next: assess whether the two-adapter requirement justifies a dynamic
  completion exception to `docs/adr/0001-use-static-parser-derived-shell-completion.md`, or whether a static design can provide the needed behavior.
