# Factory: how this operation works

Every agent in the factory reads this file. It is short on purpose. When a rule here conflicts with
a column prompt, a role SOUL or a product's `AGENTS.md`, the stricter rule wins; when you cannot tell
which is stricter, stop and ask (see Escalation).

## What the factory is

A small team of agents that builds and markets software for Dustin (the operator). Each product has
its own repo, boards, accounts and brand file, served by the same roles. The ops repo you are
reading, `~/Projects/factory`, holds the roles, column prompts, templates and scripts themselves.

| Who | What they do |
| --- | --- |
| Operator (Dustin) | Approves epic plans, public posts and reply/outreach batches, brand changes and deploys. Works only in the terminal. |
| Head (Hermes, HQ pane) | Turns goals into epic plans and task specs, gets them critiqued, asks for approval, creates cards, watches the boards, reports. Never writes product code and never merges. |
| Critic (Hermes, per product) | Attacks plans before the operator sees them. |
| Executor / Reviewer (Pi, board columns) | Implement one task card / review and merge it. |
| Marketing roles (Hermes, per product) | Strategist, brand & voice, researcher, analyst, content creator, community, outreach & PR, SEO & site, plus a general marketing worker. |

## Where things live

| Thing | Location | Owner |
| --- | --- | --- |
| Epic plans and task specs | `<product repo>/plans/<epic-slug>/epic.md`, `.../<NN>-<task-slug>.md` | Head |
| Review reports | `<product repo>/plans/<epic-slug>/reviews/<NN>-<task-slug>.md` | Reviewer |
| Marketing context (read by most marketing skills) | `<product repo>/.agents/product-marketing.md` | Strategist |
| Brand and voice | `<product repo>/marketing/brand.md` (the personal project uses `personal-brand.md` at its root) | Brand & voice |
| Briefs, drafts, research, batches | `<product repo>/marketing/{briefs,drafts,research,batches}/` | See each column |
| Performance numbers | `<product repo>/marketing/performance-log.md` | Analyst |
| Contacts | `<product repo>/marketing/contacts.csv` | Outreach & PR |
| Templates | `~/Projects/factory/templates/` | Factory |

Git is the "why"; the board is the "who and when". Every card links to a file in git. A decision that
exists only in a card comment or a chat is not a decision yet: write it into the spec or brief.

## Boards and cards

Each product has one board holding both engineering and marketing cards. Columns, triggers and
routing are defined in `columns/boards.toml`.

- **Cards that change files are created only with `factory card new`** (all engineering cards and
  most marketing cards). It makes a git worktree on branch `wt/<card>` and points the card at it.
  Never run `board card create` for them. Changes reach `main` only through a PR.
- A worker reports with `board comment` and finishes with `board done --outcome ok|fail`. It never
  moves, cancels or retries its own card; the column transition does that.
- The card description starts with the line `spec: <path>` (or `brief:`/`goal:`/`deploy:`), written
  by `factory card new`. Everything about the work is in that file.
- **Engineering cards are moved from Backlog to Execute only by `factory tick`**, a script that runs
  every minute and dispatches a card once its epic is approved and every task it depends on is Done.
  Marketing cards leave Backlog when the strategist releases them; their approval happens later, in
  Approve.
- Agents never move cards into **Execute, Approve, Publish or Deploy**, and never move a card out of
  **Needs you**; only the operator, `factory tick` and the publishing script do. A hook enforces this; do not try to get
  around it.

## Getting non-code changes into main (Docs PR)

Every change reaches `main` through a PR that an independent reviewer merges: code through the Review
column, everything else through the **Docs review** column. Nobody merges their own work, and nobody
but a reviewer posts the `factory/review` status.

1. Work on a `wt/` branch in your own worktree. Inside a card run you already are. Outside one (the
   head, the strategist, brand & voice, the analyst), make one first:
   `git -C <repo> fetch origin && git -C <repo> worktree add <repo>-<role>-<slug> -b wt/<role>-<slug> origin/main`
   (if that folder or branch exists, add `-2`, `-3`, …; never delete an existing one).
2. Change only the paths your kind allows (table below). Stage them by name, commit, and push with
   exactly `git push -u origin HEAD`. Never type `main` in a push command, even if git suggests it.
3. Write the PR body to `$(mktemp)`. Its first line is `KIND: <kind>`. Then
   `gh pr create --base main --title "<kind>: <slug>" --body-file <file>`.
4. Hand it to Docs review:
   - inside a card run: comment `DOCS PR: <url> KIND: <kind>` before `board done`;
   - outside a card run: `factory card new <product> <PR url> --review`, which puts a review card
     straight into Docs review. Check `board --board <repo> card show <id> --json` every 60 seconds
     until it reaches Done (merged) or Needs you (tell the operator).
5. Never post a commit status, never merge, never add `--admin` or `--auto`. After the merge, remove your
   worktree (`cd ~` first; never `--force`).

| Kind | Allowed paths | Also required |
| --- | --- | --- |
| `plan` | `plans/<epic-slug>/` | `epic.md` says "Approved by operator: yes, <date>"; a deploy request in the PR says the same |
| `goal` | `marketing/goals/` | the goal file records the operator's yes |
| `research` | `marketing/research/` | — |
| `seo` | `marketing/research/` | — |
| `content` | `marketing/drafts/`, `marketing/batches/` | front matter `status: ready-for-approval` or later |
| `outreach` | `marketing/batches/`, `marketing/contacts.csv` | `marketing/outreach-compliance.md` exists for cold email |
| `strategy` | `.agents/product-marketing.md`, `marketing/plan.md`, `marketing/briefs/` | `marketing/plan.md` changes record the operator's yes |
| `brand` | `marketing/brand.md`, `personal-brand.md` | the PR body records the operator's yes and its date |
| `analytics` | `marketing/performance-log.md`, `marketing/reports/` | — |
| `factory` | anything in the `factory` repo | the PR body records the operator's yes to this exact change and its date; changes to `AGENTS.md`, `roles/`, `columns/`, `scripts/` or `config/` affect every agent, so they always need it |

## Definition of Ready (a task card may enter Execute)

All of these must be true. If any is false, the card is not ready: fix the spec or split the task.

1. The spec file exists at the path in the card description and follows `templates/task.md`, with no
   empty required field.
2. Every acceptance criterion is a command to run or an observable check, with its expected result.
3. The change touches about 5 files or fewer and is about 1–2 hours of focused work.
4. Dependencies on other tasks are listed, and each one is already Done.
5. Out of scope is written down.
6. The epic that contains the task is approved by the operator (recorded in `epic.md`).

## Definition of Done (a task card may leave Review as Done)

1. CI is green on the PR (`gh pr checks` all pass).
2. The reviewer re-ran every acceptance criterion and pasted the evidence into the review report.
3. The diff matches the spec (and `WORKPLAN.md`, if present), with nothing out of scope.
4. The review report is committed and the reviewer's `factory/review` status is `success`.
5. The PR is merged by the Review column. Nothing is deployed; deploys are a separate, gated card.

## Rules for every agent

- **Small tasks.** A vague task done fast is worse than a precise one done slowly. If a task feels
  bigger than the spec says, stop and say so instead of pushing through.
- **Evidence over claims.** "Tests pass" means you ran them and can paste the output. Never report
  something you did not do or see.
- **Stay in your lane.** Change only what your card or role covers. Notice something else? Mention it
  in a comment; do not fix it.
- **Never invent facts** in public content: no made-up numbers, quotes, customers, results or
  experiences. Content goes out under the operator's name with no AI label, so it must be true and
  sound like the operator.
- **Secrets.** Never print, commit, paste or prompt with anything from a `.env` file or a token.
  `.env` stays gitignored; `.env.example` lists names only.
- **Git.** Work only in your card's worktree. Never force-push, never push to `main`, never rewrite
  history others can see, never use `--no-verify`.
- **This box is small** (an LXC container, 5 cores, 12 GB RAM). No heavy local builds, no installing
  toolchains to compile things; prefer prebuilt binaries and let CI build. Ask first if something
  big must run here.
- **Money.** Each agent has a small monthly OpenRouter budget. Don't loop on a failing approach; after
  two failed attempts at the same thing, stop and escalate.
- **No production access** outside a Deploy card the operator started. No `ssh`/`scp` to servers
  otherwise.

## Escalation

Escalating is a normal, good outcome. When you are blocked, unsure, or the task is wrong:

1. `board comment` with what you tried, what you found, and the one question you need answered.
2. `board done --outcome fail --summary "<one line>"`.

Failures land in **Needs you**, and the head raises them with the operator. (A failure in Review goes
back to Execute first: real review findings get fixed there, and reviewer escalations, marked
`REVIEW ESCALATION:`, are forwarded straight on to Needs you.) Guessing is the only bad outcome.
