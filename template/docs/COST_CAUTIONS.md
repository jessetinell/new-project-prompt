# Cost cautions

**Mandatory for every agent, on every change, forever.** Read this before you design, write or review anything that runs code, calls an API, or touches infrastructure.

## Why this file exists

We have seen it happen: an infinite loop, a retry storm, or an exponential chain of calls (a function that triggers itself, an agent that queues more work for itself, a webhook that fires another webhook) turns a cheap app into a huge cloud or AI bill overnight. Nobody notices until the invoice or the alert arrives, and by then the money is spent.

**This must never happen again.** Billing alerts are a last resort, not a plan. Every architecture decision must make runaway cost **impossible by design**, not merely unlikely.

## Hard rules

1. **Nothing triggers itself.** No code path, job, webhook, agent or database trigger may, directly or indirectly, cause itself to run again without a hard cap in between.
2. **Every paid call has a hard cap, reserved before the call.** LLM requests, agent sessions, paid APIs, emails/SMS, metered compute. The cap is checked and reserved atomically *before* spending; errors and retries still count.
3. **Retries are bounded and never multiply.** Max attempts per item (e.g. 2), then mark it failed. Never stack retries across layers (SDK retries × app retries × queue retries). Never return "try again" unless the work is actually ready.
4. **Fan-out is bounded.** Any step that creates N more steps has a fixed max N and a max depth. No recursion over unbounded data.
5. **Limits create backpressure, not spend.** When a cap is hit, work waits or fails loudly; it never falls back to an uncapped path.
6. **One worker per queue** for paid background work, enforced atomically (a single database statement), so concurrency can't duplicate spend.
7. **Agents get budgets too.** Per-session hard budgets (platform-enforced where available), bounded tool use, and instructions to stop on 401, 403 or 429, never retrying in a loop.
8. **Every paid call has a known worst case,** written down below as cost per call × cap = max per day. If you can't state the worst case, don't ship it.
9. **Hard ceilings live outside the code.** Provider spend limits and billing alerts, set in dashboards, so a bug can't bypass them.

## Before every change: the check

Answer these. If any answer is "yes" or "unsure", fix the design first.

- Can this code cause itself, or its caller, to run again?
- Can one event create more than a fixed number of follow-up events?
- Does anything retry without a max, or retry inside something that also retries?
- Is there a paid call without a cap reserved before it?
- Could two copies run at once and both spend?
- Could an outside caller (user, agent, attacker, bot) trigger this repeatedly? What stops them?
- What is the worst case per day if this runs as often as it possibly can?

## Loop patterns to watch for

| Pattern | Example | Guard |
|---|---|---|
| Self-trigger | a write fires a hook that writes again | ignore your own writes; no write-triggered paid work without a cap |
| Agent feedback loop | an agent's output queues more work for agents | agent writes never enqueue paid work; one live agent per queue |
| Webhook ping-pong | service A calls B, B calls A | idempotency keys; never call back into the caller |
| Retry storm | SDK retries × queue retries × client retries | retry in one layer only; bounded attempts |
| Exponential fan-out | each item spawns several items, recursively | max depth and max items per run |
| Endless "try again" | worker told to retry when nothing is takeable | return "done" unless work is actually ready |
| Client fetch loop | UI effect refetches on every render or state change | fixed intervals, only while visible; no fetch in render |
| Cron pile-up | a schedule starts a new run before the last ends | single-run lock; skip if already running |
| Unbounded pagination or crawling | following "next" links forever | page and item caps |
| Spoofable limits | rate limit keyed on a header the caller controls | key limits on something the caller can't forge, or one global key |

## Required limits (layered)

| Limit | Typical setting |
|---|---|
| Daily cap per paid feature | e.g. 10–30 calls or sessions per rolling 24h |
| Per-call / per-session budget | max tokens, timeout, tool uses; platform-enforced dollar budget |
| One worker per queue | one atomic database claim |
| Bounded retries | e.g. 2 attempts, then failed |
| Input size | text and file caps |
| Request rate | platform rate limiter, one global key |
| Dashboard ceilings | provider spend limit on a dedicated workspace or project, plus billing alerts |

Each limit gets a small test.

---

## This project

<!-- Fill in at setup; update on every change that adds or changes paid usage. "None yet" is a valid answer. Never delete this file. -->

### Where money can go

| Source | Triggered by | How looping is prevented |
|---|---|---|
| {{e.g. LLM calls}} | {{what triggers it}} | {{guard}} |

### Limits in code

| Limit | Setting | Where |
|---|---|---|
| {{e.g. daily cap}} | {{value}} | {{file / constant}} |

Tested: {{which tests cover the limits}}

### Worst case

{{cost per call × cap = max per day / month}}

### Hard ceilings (dashboards)

1. **{{AI provider}} spend limit:** {{amount}} on a dedicated workspace. {{link}}
2. **Cloudflare billing alerts:** {{threshold}}. https://dash.cloudflare.com/{{account_id}}/{{path}}
