# Review: task <NN> <title>

<!-- Written by the reviewer (Review column). Save as
     <product repo>/plans/<epic-slug>/reviews/<NN>-<task-slug>.md and commit it on the PR branch. -->

- **Card:** <card id>
- **PR:** <url>
- **Review round:** <number>
- **Verdict:** PASS | FAIL

## Acceptance criteria re-run

| # | Criterion | Command or check | Result | Evidence |
| --- | --- | --- | --- | --- |
| 1 | <criterion> | `<command>` | pass/fail | <short output excerpt> |

## CI

<Output of `gh pr checks`, trimmed to the check names and states.>

## Diff vs spec and WORKPLAN

- **Matches the spec:** <yes/no, with specifics>
- **Out-of-scope changes:** <none, or list with files>
- **WORKPLAN followed:** <yes/no; note deviations and whether they were justified>

## Findings

Only real problems. Each one says what, where, and why it matters.

| Severity | File:line | Problem | Required fix |
| --- | --- | --- | --- |
| blocker / should-fix / nit | `<path>:<line>` | <what is wrong> | <what to change> |

Blockers and should-fixes make the verdict FAIL. Nits alone do not.

## For the executor (if FAIL)

<The shortest list of changes that would make this pass.>
