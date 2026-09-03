# fix/sync-rm-guard — guard the destructive fallback in the sync scripts

Lane: `_todo`. Not started. Branch does not exist yet.

Prev BC: [`_done/feat/claude`](../../../_done/feat/claude/STATE.md) — this work
was recorded there as an open item and deliberately left out of scope.

## Goal

`codex/sync-to-codex.sh:14` and `codex/sync-from-codex.sh:36` both run

```bash
rm -rf "${target_dir}"/*
```

in the branch taken when `rsync` is unavailable. Shellcheck flags both as
SC2115. Fix them before the repository is handed to the team.

## Why this is worth its own branch

Small, but the two properties that matter are unrelated to size: the failure
mode is destructive and unrecoverable, and the code is a *fallback*, so it is
the path least likely to be exercised before it matters. It is also pre-existing
and unrelated to `feat/claude`, which was already through review when this was
found — folding it in would have mixed an unreviewed destructive-path change
into a documentation branch.

## Durable framing

**The real exposure is narrower than SC2115 implies.** Both scripts run
`set -euo pipefail`, so `set -u` already aborts on an *unset* variable. It does
not catch a set-but-*empty* one. `HOME=""` therefore survives and makes the
sync-to target `/.codex/skills` — bad, but not `/`. Neither script can produce a
`target_dir` of `/`: sync-to builds from `$HOME`, sync-from from `BASH_SOURCE`
plus an argument already validated as a simple directory name. So this is a
guard against the empty case, not against arbitrary values.

**Minimum fix**, which is what shellcheck itself recommends:

```diff
-  rm -rf "${target_dir}"/*
+  rm -rf "${target_dir:?}"/*
```

`:?` aborts when the variable is unset *or* empty.

**Open design question — prefer the stronger fix?** The fallback is not
equivalent to the `rsync -a --delete` branch it substitutes for: `"$dir"/*`
does not match dotfiles, so the fallback leaves hidden entries behind while
rsync deletes them. Removing and recreating the directory fixes both problems
and drops the glob entirely, sidestepping SC2115 rather than guarding it:

```bash
rm -rf "${target_dir:?}"
mkdir -p "${target_dir}"
cp -a "${source_dir}/." "${target_dir}/"
```

Recommendation is the stronger fix, since the dotfile divergence is a live
correctness bug in its own right and the two changes touch the same three
lines. Decide before implementing; do not do both in sequence.

## Constraints

- Both scripts are public. Keep them bash 3.2 clean and shellcheck clean, as
  the rest of the repository's scripts are.
- Branch off `main`, not `feat/claude`.

## Lane transitions

The lane directory is part of this BC's path, so every link that reaches it by
path breaks when the lane changes — which it will, since this BC exists to be
started. Before or with any move out of `_todo`, update:

- The reciprocal `Next BC` link in
  [`_done/feat/claude/STATE.md`](../../../_done/feat/claude/STATE.md), which
  points at `_todo/fix/sync-rm-guard/CONTEXT.md`.
- The `ai-agent-skills-context` row in the [public Branch Context
  catalog](https://github.com/mikebd/public-branch-context/blob/main/README.md),
  whose practice-example cell links this lane alongside `_done/feat/claude`.

This branch's [`README.md`](../../../README.md) names no lane and needs no
change.
