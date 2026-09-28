# Task <NN>: <title>

<!-- Written by the head (planner). Every field is required; write "none" rather than leaving one empty.
     Save as <product repo>/plans/<epic-slug>/<NN>-<task-slug>.md
     A task that fails the Definition of Ready in the factory AGENTS.md must be split or clarified. -->

- **Epic:** `plans/<epic-slug>/epic.md`
- **Size:** S (<1h) | M (1–2h)
- **Depends on:** <task numbers, or none>
- **Model override:** <none, or an OpenRouter model id with the reason>

## Context

<Why this task exists, in two or three sentences. What the executor needs to know that isn't obvious from the code.>

## Files to read first

- `<path>`: <why it matters>

## Change

<What to build or change, precisely. Name functions, endpoints, fields, UI text. Say where new code goes.>

## Acceptance criteria

Each one is a command or an observable check with its expected result. The reviewer re-runs all of them.
A criterion that can't run on the factory box (heavy build, browser, running service) must name the CI
job that verifies it: `CI job <name>` → <expected result>.

1. `<command>` → <expected result>
2. <observable check> → <expected result>

## Tests to add or update

- <test file and what it must cover, or "none, because ...">

## Out of scope

- <Related things the executor must not touch.>

## Verification commands

```bash
<the exact commands that prove the task works, e.g. lint, typecheck, test>
```

## Notes for the reviewer

<Anything risky or subtle to look at closely. "none" is fine.>
