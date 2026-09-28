# Factory scripts: required behavior

The role and column prompts rely on these behaviors. Rollout step 3 builds the scripts to match, and
tests each point.

## factory card new <product> <path> [--role <role>] [--model <openrouter id>]

- `<product>` is the repo folder name listed in `products.txt`.
- `git fetch origin`, then create the worktree on a new branch `wt/<card-slug>` from **`origin/main`**
  (never the local `main`), so a just-merged spec is present.
- Card description starts with one line naming the kind and file: `spec: <path>` (engineering task),
  `deploy: <path>`, `goal: <path>` or `brief: <path>`.
- `--role` picks the harness for the card (e.g. `hermes-<project>-strategist`); default is `pi`.
- Creates the card in Backlog. Any comment it posts starts with `FACTORY:`.
- `factory card new <product> <PR url> --review`: no new worktree. Creates a card whose description is
  `review: <PR url>`, in the PR branch's existing worktree (or a fresh one on that branch), directly in
  the **Docs review** column, and prints the card id.

## factory critic <product> <plan folder>

- Opens the product's critic profile (`<product>-critic`) in a visible Herdr pane on that folder.
- The critic writes `<plan folder>/critic.md` and ends it with a line containing only `END`.

## factory tick (every minute)

- Exits without doing anything while `~/.local/state/factory/hold` exists.
- For each product: moves a Backlog card to Execute only if its description starts with `spec:`, its
  epic's `epic.md` says "Approved by operator: yes", and every task in its "Depends on" is Done.
  Never moves `deploy:`, `goal:` or `brief:` cards.
- Resolves "Depends on: 01" to the non-archived card whose spec file starts with `01-` in the same
  epic folder.
- Sends a Herdr notification for each card newly in Needs you. Any comment it posts starts with
  `FACTORY:`.

## Publishing and send scripts (rollout step 7)

- After posting an approved item (or sending an approved outreach batch), move its card from Publish
  to **Docs review**, which merges the draft or batch PR. Until they exist, the operator does this
  move by hand.

## factory-hermes-run <profile> (Hermes roles as board harnesses)

- Configured per role in `~/.config/herdr-board/config.toml` as
  `[harness.hermes-<product>-<role>] argv = ["~/.local/bin/factory-hermes-run", "<product>-<role>"]`.
- Combines `$BOARD_SYSTEM_PROMPT` and `$BOARD_PROMPT` into one query file; runs Hermes interactively
  with `--yolo` (unattended workers can't answer approval prompts; guardrails come from hooks).
- One card at a time per remembering role: a lock per profile; a second card waits visibly.
- When the board records the run as ended, waits 30 s, then ends Hermes, releasing the lock. The pane
  keeps a shell with `hermes --resume <session>` for the operator. (Verified in step 3, 2026-09-28.)

## Governor

- Writes budget alerts (50%, 80%, 100% of the $20 monthly total) and resource alerts as single lines
  to `~/.local/state/factory/alerts.log`.
- Creates `~/.local/state/factory/hold` under memory or load pressure and removes it when healthy.

## Move guard hook

- Blocks agents from moving cards into Execute, Approve, Publish or Deploy, and out of Needs you.
- Allows `factory tick`'s moves into Execute, the publishing script's moves from Publish to Docs
  review, `factory card new --review` creating cards in Docs review, and `board card archive`.
- Nothing may post a `factory/review` status except the Review and Docs review columns.

## Timeouts

- Confirm in step 3 where herdr-board sends a card whose run times out (expected: the column's
  `on_fail`). execute.md's step 0 case 3 assumes a Review timeout lands in Execute.
