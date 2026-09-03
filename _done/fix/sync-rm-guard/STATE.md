# State — fix/sync-rm-guard

Status as of 2026-09-03: **implemented, pending review/commit.** Branch
`fix/sync-rm-guard` checked out off `main`. Carried over from
`_done/feat/claude`, where it was found and scoped out.

## Resolution

Applied the recommended stronger fix (remove-and-recreate) to both scripts,
identically:

```diff
-  rm -rf "${target_dir}"/*
+  rm -rf "${target_dir:?}"
+  mkdir -p "${target_dir}"
   cp -a "${source_dir}/." "${target_dir}/"
```

Verified:

- `shellcheck` clean on both scripts (no SC2115 or other findings).
- Exercised the no-`rsync` fallback for both scripts against scratch
  directories with a `PATH` containing no `rsync`: stale target contents
  (including a stale subdirectory) were fully replaced by source contents,
  dotfiles included.
- Confirmed `rm -rf "${target_dir:?}"` aborts (`parameter not set`, nonzero
  exit) when `target_dir` is empty, rather than deleting.

Not yet committed — holding per commit/push controls.

## Findings so far

Confirmed on `feat/claude` at `e5e1a08`:

```
codex/sync-to-codex.sh:14    rm -rf "${target_dir}"/*   SC2115
codex/sync-from-codex.sh:36  rm -rf "${target_dir}"/*   SC2115
```

`shellcheck` reports both, and nothing else in either file. Reachable only when
`command -v rsync` fails, which is why neither has ever run here.

## Next steps

1. Branch off `main`.
2. Settle the open design question in `CONTEXT.md`: `:?` guard alone, or remove
   and recreate the directory. Recommendation is the latter.
3. Apply to both scripts, keeping them identical to each other — they are near
   copies and should stay that way.
4. `shellcheck` both; confirm clean.
5. Exercise the fallback rather than reading it, which is the lesson the three
   defects on `feat/claude` all taught. Force the branch by running with a PATH
   that has no `rsync`, against a scratch `target_dir`, and confirm: contents
   replaced, dotfiles handled as intended, and an empty `target_dir` aborts
   instead of deleting.

## Acceptance

- Both scripts shellcheck clean.
- An empty `target_dir` aborts with an error rather than deleting anything.
- The no-`rsync` fallback leaves the target matching the source, dotfiles
  included, verified by running it.
