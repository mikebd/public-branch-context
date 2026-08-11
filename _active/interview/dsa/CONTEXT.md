# DSA interview practice

## Purpose

This active Branch Context manages an ongoing sequence of data-structure and
algorithm interview questions. The developer requests one of `easy`, `medium`,
or `hard`; each approved explicit Plan Mode question then presents a single
spoiler-free problem to solve in Go.

The series starts fresh. Existing exercises elsewhere in this repository do
not count as prior coverage and do not constrain question selection.

## Question plans and answer paths

- Administrative setup is captured as
  `plans/001-initialize-dsa-interview-workflow.md`. It is the one-time plan
  that does not issue an interview question or have an answer directory.
- The first interview question is plan `002`.
- A new question requires an explicit Plan Mode request containing exactly one
  difficulty: `easy`, `medium`, or `hard`. Ask for the difficulty when it is
  absent; do not choose a default.
- Store each approved question as `plans/NNN-<neutral-slug>.md`.
- Build its answer under
  `internal/interview/dsa/<difficulty>/NNN-<neutral-slug>/`, using the exact
  same number and slug as the plan.
- When implementing an issued question plan, create that exact answer
  directory. It is the only product-tree setup created while issuing a
  question.
- Number new question plans monotonically using the next available
  three-digit number. Do not renumber or overwrite existing plans.

Each newly numbered question plan initially contains exactly one question and
only the material needed to understand the prompt:

- the requested difficulty and matching answer path
- a self-contained problem statement
- problem-inherent constraints
- optional input/output examples written as neutral pseudocode, but only when
  they clarify behavior without suggesting a solution

The developer owns API design, implementation, and tests. Apart from the
required empty answer directory, do not create a Go signature, package files,
starter implementation, test cases, implementation steps, or acceptance
checklist when issuing a question.

## Spoiler policy

Question plans and filenames must not disclose or suggest:

- the expected data structure or algorithm
- solution techniques or key concepts
- target time or space complexity
- hints, solution outlines, or answers

Use a neutral problem-domain slug and title rather than a technique-oriented
name. Necessary input and output behavior may be stated, but examples must not
encode an implementation approach.

## Coverage rotation

Use the retrospective question ledger in `STATE.md` to broaden coverage across
the new DSA series. Prefer an as-yet-uncovered primary concept for each newly
numbered question. Do not record a pending question's intended concepts in the
plan, `STATE.md`, filenames, or other BC artifacts before evaluation.

After evaluation, record the concepts demonstrated by the submitted answer,
along with observed strengths and gaps. This retrospective record becomes the
coverage input for selecting later questions.

## Evaluation workflow

The developer starts a separate turn after building an answer. Evaluation is
review-only by default:

- inspect the implementation and tests
- run relevant tests when feasible
- assess correctness, time and space complexity, edge cases, test quality, and
  idiomatic Go
- report findings without modifying the answer unless explicitly requested
- update `STATE.md` with the evaluation status, retrospectively identified
  concepts, strengths, and gaps

## Follow-up workflow

When explicitly requested after evaluation, append one broader or deeper
follow-up prompt to the originating plan under the next `Follow-up` heading.
The follow-up:

- remains within the original `easy`, `medium`, or `hard` difficulty band
- keeps the original plan number, slug, and answer directory
- does not consume a new plan number
- follows the same spoiler policy

Update `STATE.md` when the follow-up is issued and again after its answer is
evaluated. A follow-up may intentionally deepen an already covered concept;
the unseen-concept preference applies to newly numbered questions.
