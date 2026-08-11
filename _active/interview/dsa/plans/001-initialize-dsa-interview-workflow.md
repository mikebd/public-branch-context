# Initialize the DSA Interview Branch Context

## Summary

Create the active BC at `.context/_active/interview/dsa/` for an ongoing,
spoiler-free DSA interview workflow. Capture this administrative setup as
`plans/001-initialize-dsa-interview-workflow.md`; it is the one-time exception
to question-bearing plans. The first interview question will be plan `002`.

## BC Structure and Rules

- Add `CONTEXT.md`, `STATE.md`, and
  `plans/001-initialize-dsa-interview-workflow.md`.
- Update the BC-root README so it no longer says no lanes exist.
- Support only `easy`, `medium`, and `hard`; ask when a question request omits
  difficulty.
- Store future questions as `plans/NNN-<neutral-slug>.md`, with answers under
  `internal/interview/dsa/<difficulty>/NNN-<neutral-slug>/`.
- When implementing an issued question plan, create its matching empty answer
  directory. Do not create any Go files, APIs, or tests at that stage.
- Start concept coverage from scratch at question `002`; ignore all older
  repository exercises.
- Keep an after-evaluation concept ledger in `STATE.md`. Never record the
  pending question's concepts before evaluation.

## Interview Workflow

- Each newly numbered question plan initially issues exactly one prompt
  containing:
  - The requested difficulty and matching answer path.
  - A self-contained problem statement and inherent constraints.
  - Optional pseudocode examples only when they clarify behavior without
    suggesting a data structure or algorithm.
- Do not include solution hints, key concepts, technique-oriented titles or
  slugs, target complexity, Go signatures, Go scaffolding, tests,
  implementation steps, or answers.
- Prefer concepts not yet represented in the post-evaluation ledger so the new
  series develops broad coverage.
- On an evaluation turn, review without editing: run relevant tests and assess
  correctness, complexity, edge cases, test quality, and idiomatic Go. Then
  update `STATE.md` with status, retrospectively identified concepts,
  strengths, and gaps.
- On an explicitly requested follow-up, append one broader or deeper prompt to
  the original plan, retain its number, difficulty, slug, and answer directory,
  and preserve the same no-hints rule. Follow-ups do not consume another plan
  number.

## Validation

- Confirm only `_active/interview/dsa/` was created and no unrelated lanes or
  product-code directories were added.
- Confirm setup plan `001` exists, `STATE.md` identifies `002` as next, and the
  coverage ledger starts empty.
- Verify the BC documents contain the exact easy/medium/hard terminology and
  spoiler restrictions.
- For each issued question, confirm its declared answer path exists and has no
  agent-created Go source or test files.
- Run `git -C .context diff --check` and report the separate BC repository
  status.

## Assumptions

- Setup plan `001` intentionally contains no interview question or matching
  answer directory.
- Follow-ups remain within the original difficulty band.
- BC changes remain uncommitted and unpushed unless separately authorized.
