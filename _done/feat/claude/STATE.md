# State — feat/claude

Status as of 2026-09-03: eleven commits, pushed. PR #1 open and mergeable,
three CodeRabbit review rounds addressed and all nine threads resolved. The
eleventh commit came from a config-alignment review, not from CodeRabbit, and
found the largest defect of the branch.

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
1d02432 fix(claude): apply Claude Code's real matcher rules in the hook probe
17e26ba fix(claude): make the allowlist match what the RTK hook actually produces
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

**A Claude Code hook matcher is read three different ways**, decided by the
characters in it. `*`, empty and absent match every tool. Only letters, digits,
`_`, `-`, spaces, `,` and `|`: an exact name, or a list split on `|` or `,`.
Anything else: an unanchored JavaScript regex. The unanchored part is the trap —
`ash.*` is a valid Bash registration. Source is Claude Code's hooks reference;
comma separators need v2.1.191+, hyphens in the exact set v2.1.195+.

**rtk installs no hook script of its own** (rtk 0.45.0). `rtk init -g
--hook-only --auto-patch` writes the `hooks.PreToolUse` registration into
`settings.json` and nothing else — there is no `<agent-home>/hooks/` directory
and no `rtk-rewrite.sh`. `settings.json` is therefore the sole evidence that the
hook will fire, which is what makes the third defect below a defect.

**`rtk gain --history` is per-user and machine-wide** (`~/.local/share/rtk/history.db`).
Entries mix working directories and sessions; a line there is not evidence
about the current workspace. A session misread one this way.

## Five defects in the guard/install pair

All five survived tests that asserted on file *contents* or on a script's own
shape, rather than on the behaviour the artifact exists for. Two were found by
running things for real; three by review.

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

5. And the fix for 4 was also incomplete. `matches_bash` tested the matcher
   with `re.fullmatch`, which is neither of the two rules Claude Code uses. Its
   documented behaviour, verified against the hooks reference rather than
   assumed: `*`, empty and absent matchers fire on every tool; a matcher of only
   letters, digits, `_`, `-`, spaces, `,` and `|` is an exact name or a list
   split on `|` or `,`; anything else is an **unanchored** JavaScript regex. So
   `Edit, Bash` and `ash.*` are both live registrations that `fullmatch`
   reported as missing. Fixed in `1d02432`.

   Direction matters here and was benign: every one of these errors reported a
   working hook as absent, so the guard failed closed. That is the opposite of
   defects 1 and 3, which reported a broken setup as fine.

   The pattern across all five is one thing, and it is not carelessness about
   any individual check. Each fix asserted the property the previous defect had
   violated, and stopped there. Defect 3 replaced "does a file exist" with "is
   there a PreToolUse entry" — a better question, still not the real one.
   Defect 4 added "under a Bash matcher" without asking what Claude Code means
   by a matcher. The real property, stated in full: *Claude Code will run
   `rtk hook claude` on a Bash tool call, from a path it can resolve without
   help.* Every clause of that has now been a defect. The habit worth keeping
   is to write the property out before testing any part of it.

   `claude/scripts/rtk-hook-probe-test.py` (added `1d02432`, 19 stdlib tests)
   now pins the whole sentence. It was checked against the pre-fix code rather
   than assumed to work: reverting `matches_bash` fails exactly the two cases
   that round was about.

## The hook and the allowlist are not independent

Found 2026-09-03 by asking whether this machine's config matched the repo, and
the most consequential defect on the branch: `settings.json.example` was largely
inert for precisely the users who followed the Claude setup here.

`rtk hook claude` returns the rewritten command as `updatedInput`, and Claude
Code evaluates `PreToolUse` hooks *before* the permission decision. Permission
rules therefore match the rewritten string. Established by feeding the hook a
real PreToolUse payload rather than reading the docs and inferring: the reply
carries `updatedInput` and no `permissionDecision`, so the hook rewrites without
approving anything, and the allowlist then sees `rtk git status` where the rule
said `git status`.

14 of 18 baseline rules and 4 of 7 opt-in rules never fired. Two rewrites are
worth remembering: `npx playwright test` -> `rtk playwright test` drops the
`npx`, while `npx cypress run` keeps it. `git merge`, `git rebase`, `npm test`
and `ng test` are not rewritten at all. Table is rtk 0.45.0; an upgrade that
changes a rewrite turns a working rule into a dead one with no error.

Fixed in `17e26ba` by listing both forms, so the example behaves the same with
or without the hook.

**The sharper half.** `*` spans spaces, so a single `Bash(rtk git *)` covers
`rtk git commit` and `rtk git push`. That silently removes the manual-review
hold that `EXECUTION_MODEL.md` says the permission prompt *is*. Not
hypothetical: it was the state of `.claude/settings.local.json` on this machine,
alongside a `Bash(gh pr *)` rule that was simultaneously inert (never matched
`rtk gh pr view`) and over-broad (would have covered `gh pr create` and
`gh pr merge` had it matched). Both replaced with per-subcommand rules in both
forms.

This is the same failure mode as the unpinned hook and the false all-clear
guard, one layer up: a control that reads as present and does nothing. Three of
them now, in three different mechanisms.

## Machine state

`~/.claude/LOCAL-MACHINE.md` was created 2026-09-03 and now holds the
machine-local facts previously scattered through this file: rtk's Linuxbrew path
and why the pin matters, the guard's python3 requirement, the rewrite-before-
permissions interaction, and which `settings.json` backup is which.

The misleading backup is gone. `settings.json.20260902220159.bak` held the
hook with the bare unpinned command, so restoring it would have reinstated the
silent-failure mode; deleted 2026-09-03. `~/.claude/settings.json.bak`
(`theme` only) is the genuine pre-install state and was kept — an earlier note
here recorded the first file without mentioning that a clean one also existed.

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
- **Rewrite table drift.** The raw/rtk rule pairs in `settings.json.example`
  are correct for rtk 0.45.0 and were verified by running the hook. Nothing
  re-checks them, so an rtk upgrade that changes a rewrite silently retires a
  rule. A test comparing `rtk hook check` output against the example would
  catch it; not written.

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
