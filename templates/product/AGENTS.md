# {product}: rules for agents

Read `~/Projects/factory/AGENTS.md` first; everything there applies here. This file adds what is
specific to {product}. Where they differ, the stricter rule wins.

<!-- The operator fills in every TODO before the first plan. Until then, the head asks the operator
     instead of guessing. -->

## What {product} is

TODO: one paragraph: who it is for and what it does.

## Components

TODO: a table of folders, what each is, where it runs and its language.

## Checks you can run here

CI runs a secret scan on every PR. TODO: add the tests and lint commands to CI and list them here.
Acceptance criteria must be checkable on the factory box or in CI; a spec names how anything else is
verified.

## Branches: main and production

- **`main`** is where all work lands, through PRs reviewed and merged by the Review or Docs review
  column.
- **`production`** is exactly what is live. Only an approved Deploy card moves it, by promoting a
  reviewed commit from `main`. Agents never push to it or open PRs against it.

## Deploying

TODO: what deploys from `production`, and how. Until this is filled in, nothing deploys.

## Secrets

- Never commit or print anything in `.env` or `*.env` files. `.env.example` lists the names only.

## Operator-only files

Agents never change `.github/workflows/`, `AGENTS.md` or `CLAUDE.md`. A change there goes to the
operator.
