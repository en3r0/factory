# Marketing skill repos — audit (2026-09-25)

Six repos were audited by parallel subagents (clones in the Claude session scratchpad). Key claims were spot-checked afterwards.
Model context: everything runs on DeepSeek v4.1 Flash. Checklist- and template-driven skills rate higher than judgment-heavy ones.

## Verdict per repo

| Repo | License | What it really is | Verdict |
|---|---|---|---|
| [iannuttall/seo](https://github.com/iannuttall/seo) | Apache-2.0 | npm CLI + MCP server (`seo` v0.2.40) with **one** router skill over ~70 deterministic, evidence-backed reports. Needs Node 22+. | **SEO backbone.** Pi can call the CLI with `--json`, no MCP needed. |
| [coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills) | MIT | 50 prompt-only skills (no scripts), very actively updated; a shared `.agents/product-marketing.md` context file that ~all skills read. | **Main marketing library.** Its SEO skills are backup, except `ai-seo`, `programmatic-seo` and `directory-submissions`, which add distinct value. |
| [phuryn/pm-skills](https://github.com/phuryn/pm-skills) | MIT | 69 PM skills + 42 Claude-only slash commands (commands are just recipes). | **Planning/critic side** (strategy-red-team, pre-mortem, create-prd, user/job stories) + positioning/GTM (beachhead, ICP, battlecard). |
| [blader/humanizer](https://github.com/blader/humanizer) | MIT | One skill: 25 AI-writing tells in 5 categories, 4-step rewrite, hard no-invention rule. | **Editor pass on all public copy.** Needs the user's voice sample. Half its rules are regex-lintable. |
| [ericosiu/ai-marketing-skills](https://github.com/ericosiu/ai-marketing-skills) | MIT | 35 skills from an agency; heavy on video + B2B sales. 9 files hard-code the Anthropic SDK; telemetry pings GitHub; `clone-site` needs Chrome MCP. | **Cherry-pick only:** `x-longform-post`, `content-ops` expert panel, `shortform-idea-grill`, `conversion-ops`, `personal-strategic-signal-intelligence`, `growth-engine` (later), `security/sanitizer.py`. |
| [Affitor/affiliate-skills](https://github.com/Affitor/affiliate-skills) | MIT | 52 skills, **all** for being an affiliate for other companies' products. None for running your own program. Adds a "Powered by Affitor" footer + UTM tags; `reddit-post-writer` coaches disguised promotion. | **Skip** unless you want affiliate income. marketingskills' `referrals` covers running your own program. |

## Top skills for software marketing (ranked)
1. marketingskills `product-marketing` — the shared context file everything else reads.
2. iannuttall `seo report` / `top-fixes` / `site-crawl` — free, URL-only, ranked fixes.
3. iannuttall `ai-readiness` / `agent-readiness` / `geo-gaps` + `generate-llms-txt` — AI-search discoverability, free.
4. marketingskills `launch` + `directory-submissions` — launch plan, Product Hunt, directories.
5. pm-skills `beachhead-segment`, `ideal-customer-profile`, `positioning-ideas`, `competitive-battlecard`.
6. marketingskills `copywriting`, `cro`, `ai-seo`, `customer-research`, `competitor-profiling`.
7. marketingskills `marketing-loops` — designs recurring marketing work; maps onto cron + board cards.
8. iannuttall `quick-wins` / `decaying-pages` / `technical-watch` — recurring, needs Google Search Console.
9. ai-marketing `conversion-ops` (CRO audit, no paid keys).

## Top skills for personal brand (ranked)
1. humanizer + your voice sample — the difference between "you" and "AI slop".
2. marketingskills `social` (+ `content-strategy`, `public-relations`).
3. ai-marketing `x-longform-post` (founder-voice template) and `content-ops` expert-panel scoring.
4. ai-marketing `personal-strategic-signal-intelligence` (needs a feed of your reading/notes).
5. iannuttall `ai-mention-research` / `ai-prompt-observations` — what AIs say about you (paid: DataForSEO).
6. ai-marketing `shortform-idea-grill` → ClipHuman (dogfood) for video.

## Gaps no repo fills
- No **personal-brand context file**. Create `personal-brand.md` modeled on `product-marketing.md` (voice samples, positioning, pillars, channels, story, no-go topics).
- No skill for **running your own affiliate program** beyond marketingskills `referrals`.

## Costs
Free: everything prompt-only; Google Search Console/GA via a **service account** (the VM has no browser, so skip the OAuth flow); Bing; CrUX.
Paid, optional: DataForSEO (keyword/competitor/AI-mention reports), Ahrefs/Semrush, Metricool, Imagen, and B2B sales APIs (skip).

## Porting notes
- Strip the Affitor footer/UTM tags and the Single Brain calls-to-action if anything is reused.
- Patch ai-marketing scripts from the `anthropic` SDK to an OpenAI-compatible client on OpenRouter; disable `telemetry/version_check.py`.
- pm-skills commands are Claude-only; treat them as recipes.
- marketingskills has many dated specifics (e.g. platform changes as of Aug 2026). Re-verify them periodically.
