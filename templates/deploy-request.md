# Deploy request: <product> <what>

<!-- Written by the head when merged work is ready to go live. The operator reads this, then moves the
     card into Deploy personally. Save as <product repo>/plans/<epic-slug>/deploy-<YYYY-MM-DD>.md
     Every field is required. Read the product AGENTS.md deploy section first: it decides which steps an
     agent may run. Anything it reserves for the operator goes under "Operator steps", never under
     "Deploy steps", or the Deploy column will fail precondition 4 and hand it straight back. -->

- **Product:** <product>
- **Environment:** <production | staging | ...>
- **Requested by:** head, <YYYY-MM-DD>
- **Approved by operator:** <no | yes, YYYY-MM-DD>

## What goes out

| PR | Title | Merged |
| --- | --- | --- |
| <#> | <title> | <date> |

Commit range: `<from-sha>..<to-sha>`

Promote: `<to-sha>` on `main` → `production` (for products that use a `production` branch; see the
product's `AGENTS.md`). `<from-sha>` is the current `production`.

## User-visible changes

- <change>

## Risk

<low | medium | high>, because <reason>. Database migrations: <none | list, and whether they are reversible>.

## Deploy steps

<The exact commands an agent may run, from the product's AGENTS.md deploy section. No improvising.
When the product reserves a component's steps for the operator, this section holds only the promote
(and the site step) — the component steps go under "Operator steps" below.>

```bash
<commands>
```

## Operator steps (reference; agents never run these)

<Write "none" when every step is one an agent may run — the common case for a site-only deploy.
Otherwise the exact commands per component, filled in with <to-sha> and copied from the product
AGENTS.md so the operator can paste them. The Deploy column runs "Deploy steps", then hands the card
back with these instead of running them.>

```bash
<commands>
```

## Checks after deploy

<Mark each check with who can run it. An agent can run the promote and anything over the public URL; a
check that needs ssh or a browser is the operator's, and the Deploy column lists it in its comment
rather than attempting it.>

**After the promote (agent):**

1. `<command or URL>` → <expected result>

**After the operator steps (operator):**

2. `<command or URL>` → <expected result>

## Rollback

<The exact commands to go back to the previous version, and how long it takes.>

```bash
<commands>
```

## Result

<Filled in by the Deploy column when it ran every step: what ran, check results with evidence, and final
state. When the product reserves steps for the operator, the column stops after the promote and hands
the rest back on the card; the head records the outcome, including the operator's check results, in the
epic's Outcome once the operator confirms.>
