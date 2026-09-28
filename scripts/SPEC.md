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

## factory ci <PR>

- Waits for the GitHub Actions runs on the PR's head commit via the Actions API (works on private
  repos with a fine-grained token; `gh pr checks` needs a checks permission those tokens lack).
- Prints JSON; exit 0 all passed, 1 a run failed, 3 no CI within 5 minutes, 4 timeout.
- Agents never re-run CI (the token has no Actions write).

## factory critic <product> <plan folder>

- Opens the product's critic profile (`<product>-critic`) in a visible Herdr pane on that folder.
- The critic writes `<plan folder>/critic.md` and ends it with a line containing only `END`.

## factory product new <name> [--dry-run]

- The operator creates `github.com/en3r0/<name>` first and adds it to the fine-grained GitHub token's
  repository access; the command stops if the repo does not exist (the token cannot create repos) and
  explains the token step if a push is refused.
- Safe to re-run: each step checks what exists and does only what is missing. Steps: clone to
  `~/Projects/<name>` (or check an existing clone's origin), set `core.hooksPath` to the factory
  githooks, add the repo to `products.txt`, create the board and every column from `boards.toml`,
  install every role in `roles/` except `head` as the profile `<name>-<role>` with the OpenRouter key
  in its `.env` (chmod 600), and add a board harness for every Hermes role except the critic to
  `config/herdr-board.toml`.
- Empty repo, run by the operator: commits the starter files from `templates/product/` to `main`,
  pushes, and creates `production` at the same commit. Run by an agent, that step is left for the
  operator (agents never push to `main`).
- Existing code: the default branch must already be `main`. No files are written; a missing
  `AGENTS.md` means onboarding through a PR the operator reviews.
- Public repo: checks for a ruleset and lists the steps if there is none. Private: guard-only.
- Hermes is called without `HERMES_HOME`, which would otherwise hide every profile but the caller's.
- Ends with a numbered "Left for the operator" list.

## factory tick (every minute)

- First keeps the factory itself current: fast-forwards `~/Projects/factory` to `origin/main` when the
  checkout has no local edits (so merged factory PRs take effect), and when any file in `columns/` is
  newer than the last sync, runs `factory board setup` on every product's existing columns. That
  refreshes each column's prompt, harness, trigger, timeout and session from `boards.toml` and the
  prompt files.

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
- One card at a time per remembering role: a lock per profile; a second card waits visibly. Profiles
  with `memory_enabled: false` (the general marketer) take no lock and run in parallel.
- When the board records the run as ended, waits 30 s, then ends Hermes, releasing the lock. The pane
  keeps a shell with `hermes --resume <session>` for the operator. (Verified in step 3, 2026-09-28.)

## Governor

- Writes budget alerts (50%, 80%, 100% of the $20 monthly total) and resource alerts as single lines
  to `~/.local/state/factory/alerts.log`.
- Creates `~/.local/state/factory/hold` under memory or load pressure and removes it when healthy.

## Move guard hook

- Blocks agents from moving cards into Execute, Approve, Publish or Deploy, and out of Needs you.
- Allows `factory tick`'s moves into Execute, the publishing script's moves from Publish to Docs
  review, and `factory card new --review` creating cards in Docs review.
- Blocks agents from creating, editing, reordering, archiving, restoring or deleting boards, columns,
  projects and cards, and from changing the board selection. The factory scripts call the real
  `board` binary (not the shim), so `factory product new`, `board setup` and `cleanup` still work when
  the head runs them.
- For everyone, the operator included: `board board archive|rename`, `board project archive`,
  `board column delete` and `board card archive|delete` must name a target, `column delete` must also
  pass `--board`, and `--board`/`--project` must never be empty. herdr-board otherwise falls back to
  the selected board (an empty shell variable archived the wrong board once, 2026-09-28).
- Nothing may post a `factory/review` status except the Review and Docs review columns.

## Timeouts

- Confirm in step 3 where herdr-board sends a card whose run times out (expected: the column's
  `on_fail`). execute.md's step 0 case 3 assumes a Review timeout lands in Execute.
