# State — feat/claude

Status as of 2026-09-03: nine commits, pushed. PR #1 open and mergeable, two
CodeRabbit review rounds addressed and all eight threads resolved.

```
b0394b4 chore: normalize home-path style and drop a hardcoded home path
aacfc7c refactor(rmar): make the runtime contract agent-neutral and add Claude Code
41de51a docs(rtk): split enforcement by agent and add pinned hook install
412448a docs(codex): record that some surfaces silently ignore developer_instructions
3f07eff fix(claude): use POSIX character classes and honor CLAUDE_CONFIG_DIR
5d74f75 fix(rtk): back up settings.json before the first mutation, not the last
e509136 fix(claude): tighten RTK hook detection and drop installs from the allowlist
e5e1a08 fix(codex): honor CODEX_HOME in the madsense-local-backend-auth commands
9191020 fix(claude): require a Bash matcher and an absolute path in the hook check
```

## Verified findings (2026-09-02, Linux x86_64, rtk 0.45.0)

**RTK hook rewrite behaviour.** Established with `rtk hook check '<cmd>'`:

```
git status                   -> rtk git status
cat README.md                -> rtk read README.md
env GOCACHE=/tmp/gc go test  -> env GOCACHE=/tmp/gc rtk go test   (prefix kept)
rtk grep -n foo .            -> unchanged                          (no double-wrap)
shared/scripts/rtk_proxy.sh  -> no rewrite                         (fallback intact)
```

`rtk init -g --hook-only --auto-patch` writes only the settings.json patch;
plain `rtk init -g` also writes `<agent-home>/RTK.md` and an `@RTK.md` import.
rtk honours `CLAUDE_CONFIG_DIR` (hook landed in a custom dir, not `~/.claude`).

**`rtk rg` supersedes the `--glob` workaround.** `rtk grep --glob` still fails
(delegates to GNU grep); `rtk rg --glob` works and was 40% smaller than raw
`rg` on a sample search. The old remedy — a raw `rg` — is unreachable under the
hook, which rewrites it.

**ChatGPT desktop Codex ignores `developer_instructions`.** Shares `~/.codex`,
`config.toml` present, key present, runs locally, `rtk` on PATH — and the text
never reaches the model. Asked what `DOC_MAP.md` governs, it answered "don't
know". Network egress blocked (`curl` -> 000); no approval prompt for a command
block; machine-wide reads, writes confined to the workspace. Agent-home
`~/.codex/AGENTS.md` also not picked up. Recorded in
`codex/EXECUTION_MODEL.md`.

**Claude activation works, including user-scope reach.** A fresh session in an
unrelated repository had all three imports loaded, correctly reported
`DOC_MAP.md` and `RTK.md` as referenced-but-not-loaded, resolved `<agent-home>`
via `CLAUDE_CONFIG_DIR`, and treated BC as sourced-but-not-enabled pending root
resolution. Hook confirmed firing: a plain `git status` recorded as
`rtk git status`.

**A Claude Code hook matcher is a regex against the tool name.** An absent,
empty, or `*` matcher selects every tool; `Bash|Edit` selects Bash. Checking for
the exact string `Bash` would reject valid hand-edited registrations, so the
probe applies the matcher as a pattern.

**rtk installs no hook script of its own** (rtk 0.45.0). `rtk init -g
--hook-only --auto-patch` writes the `hooks.PreToolUse` registration into
`settings.json` and nothing else — there is no `<agent-home>/hooks/` directory
and no `rtk-rewrite.sh`. `settings.json` is therefore the sole evidence that the
hook will fire, which is what makes the third defect below a defect.

**`rtk gain --history` is per-user and machine-wide** (`~/.local/share/rtk/history.db`).
Entries mix working directories and sessions; a line there is not evidence
about the current workspace. A session misread one this way.

## Four defects in the guard/install pair

All four survived tests that asserted on file *contents* or on a script's own
shape, rather than on the behaviour the artifact exists for. Two were found by
running things for real; two by review.

1. `rtk-guard.sh` matched `@RTK.md` with `grep -qE '^\s*@...'`. `\s` is a GNU
   extension; on macOS the pattern never matches, so the script would report
   "no duplicate artifacts" while the import sat in `CLAUDE.md` — a false
   all-clear. Found by a Codex review. Fixed in `3f07eff`. It survived an
   earlier portability sweep that checked `sed -i`, `\+`, `grep -P`, `date -d`,
   `stat -c`, `realpath`, `readlink -f` — a denylist, which cannot be complete.
2. `rtk-install.sh` backed up `settings.json` *after* running `rtk init`, so
   the backup already contained the hook. Restoring it undid the pin and
   nothing else. Found on the first real run. Fixed in `5d74f75` with a
   `take_backup` helper called before the first mutation; tested by restoring,
   not by inspecting.
3. `rtk-guard.sh` reported the hook as installed when `<agent-home>/hooks/`
   `rtk-rewrite.sh` existed, or when `settings.json` contained the string `rtk`
   anywhere. The first branch tests for an artifact rtk never writes (see the
   finding above); the second matches an unrelated env var or comment. Together
   they meant the guard could exit 0 on a machine where nothing rewrites
   commands — the same false all-clear as defect 1, reached by a different
   route. Found by a CodeRabbit review of PR #1. Fixed in `e509136`: detection
   now requires a real `hooks.PreToolUse` registration of `rtk hook claude`,
   parsed by `claude/scripts/rtk-hook-probe.py`, with a grep fallback when
   Python 3 is absent. The same commit fixed a second-order bug the review also
   caught — with no duplicate `RTK.md` present the script exited 0 regardless of
   hook state, so tightening detection alone would not have made a clean machine
   fail.

   Verified against five configurations rather than by re-reading: absent
   `settings.json`, stray `rtk` text with no hook, bare registration, pinned
   registration plus a duplicate `RTK.md`, and this machine's real config. The
   no-Python fallback was checked separately against the three parseable ones.
4. The fix for 3 was itself incomplete, and the second review round found both
   gaps. The probe read `hooks.PreToolUse` without looking at each entry's
   `matcher`, so a hook registered for another tool counted as installed even
   though it never sees a shell command. And `pinned` was decided by basename,
   which accepted `./rtk` and `bin/rtk` — paths that resolve against a working
   directory the hook cannot predict, so no less fragile than the bare name.
   Fixed in `9191020`; both now verified across twelve settings files.

   The pattern across all four is one thing, and it is not carelessness about
   any individual check. Each fix asserted the property the previous defect
   had violated, and stopped there. Defect 3 replaced "does a file exist" with
   "is there a PreToolUse entry" — a better question that still was not the
   real one, which is "will Claude Code run this on a Bash command, from a
   path it can resolve". The useful habit is to state the property the artifact
   exists to guarantee, in full, before testing any part of it.

## Open items

- **macOS validation — the main one.** Nothing here has run on macOS, where the
  whole team is. Minimum: `./claude/scripts/rtk-install.sh --dry-run`. Better:
  a real install followed by restoring the `.bak`, which exercises the path
  that actually failed above.
- **`rtk init --codex` non-global / project-root `AGENTS.md`** as the ChatGPT
  desktop activation path is unverified. Probe is in `codex/EXECUTION_MODEL.md`.
- **Pre-existing `SC2115`** in `codex/sync-to-codex.sh:14` and
  `sync-from-codex.sh:36`: `rm -rf "${target_dir}"/*`. Still unfixed; still out
  of scope for this branch. Reachable only in the no-`rsync` fallback, and
  `set -u` catches an unset `$HOME` — but not an empty one, which would make the
  sync-to target `/.codex/skills`. Fix is `"${target_dir:?}"/*`.

  Publishing this note in a public catalog discloses nothing: both scripts are
  already public in `mikebd/ai-agent-skills`, so a reader who cares can see the
  line itself. Worth stating the asymmetry the other way round, though —
  describing a known destructive defect publicly while leaving it unfixed is the
  weaker position of the two, and the fix is two characters. Recommend fixing it
  on its own branch before the repo goes to the team, at which point this entry
  becomes history.

  Next BC: [`_todo/fix/sync-rm-guard`](../../../_todo/fix/sync-rm-guard/CONTEXT.md),
  created 2026-09-03 to carry it. It also records a second-order bug found while
  scoping: the fallback does not match dotfiles, so it is not equivalent to the
  `rsync --delete` branch it substitutes for.
- **`AI_AGENT_LAUNCHER.md:48`** points launcher candidate 3 at
  `~/src/mikebd/py/scripts`, which resolves to nothing in `ai-agent-skills` and
  is unplaceable for a reader outside this machine — the line reads as though it
  were repo-relative.

  Resolved 2026-09-03: it is a local checkout of `github.com/mikebd/py-scripts`,
  which is **public**. The local directory layout (`py/scripts`) simply differs
  from the repository name (`py-scripts`), which is what makes the path look
  private. An earlier draft of this entry guessed the repo was unpublished; that
  was wrong, and it changes the remedy.

  Harmless at runtime either way: candidates are probed with `--version` and a
  failing one is skipped, so the resolver falls through to candidate 4. The cost
  is documentation only. Because the source *is* public, the fix is better than
  genericising the line — name the repository so a reader can go get it, and
  demote the absolute path to the author's local default. Out of scope for this
  branch; small enough to fold into whichever branch next touches that doc.
- **This machine's `~/.claude/settings.json.<ts>.bak`** was written by the
  pre-`5d74f75` code, so it holds the post-`rtk init` state. `rtk init -g
  --uninstall` is the reliable removal path.

## Handoff

Machine is activated: hook installed and pinned, `~/.claude/CLAUDE.md` importing
the runtime contract, the Claude overlay and BC. `./claude/scripts/rtk-guard.sh`
exits 0 — and now means something, per defect 3.

`.context` resolves in the repo as of 2026-09-03, so BC is live for sessions
working in it; this BC is the one they will find. It stays in `_done`: the work
is complete and the path is referenced from PR #1.

The guard now requires python3 and has no grep fallback — worth knowing before
running it on a machine that lacks one, where it reports "cannot verify" and
exits 1 by design rather than by failure.

Next action is the PR itself. Two review rounds are closed and macOS validation
above remains the one open item that matters before the repo goes to the team.
