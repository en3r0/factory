# Deploy request: <product> <what>

<!-- Written by the head when merged work is ready to go live. The operator reads this, then moves the
     card into Deploy personally. Save as <product repo>/plans/<epic-slug>/deploy-<YYYY-MM-DD>.md
     Every field is required. -->

- **Product:** <product>
- **Environment:** <production | staging | ...>
- **Requested by:** head, <YYYY-MM-DD>
- **Approved by operator:** <no | yes, YYYY-MM-DD>

## What goes out

| PR | Title | Merged |
| --- | --- | --- |
| <#> | <title> | <date> |

Commit range: `<from-sha>..<to-sha>`

## User-visible changes

- <change>

## Risk

<low | medium | high>, because <reason>. Database migrations: <none | list, and whether they are reversible>.

## Deploy steps

<The exact commands, from the product's AGENTS.md deploy section. No improvising.>

```bash
<commands>
```

## Checks after deploy

1. `<command or URL>` → <expected result>

## Rollback

<The exact commands to go back to the previous version, and how long it takes.>

```bash
<commands>
```

## Result

<Filled in by the Deploy column: what ran, check results with evidence, and final state.>
