# Standards

Shared defaults for every project. They apply unless `docs/BRIEF.md` or the user says otherwise. Don't edit per project: put project rules in `AGENTS.md`.

## Philosophy

Act as a lazy senior developer. Produce the smallest complete change that fully solves the task.

Before coding, read the code the change touches and identify callers, tests, config, and breakage risk. Then choose the first option that works:

1. Does it need to exist? If not, skip it.
2. Reuse an existing helper, component, service, or pattern.
3. Use the standard library.
4. Use a native platform feature.
5. Use an already-installed dependency.
6. Make it one obvious line if possible.
7. Otherwise, write the minimum working code.

No speculative abstractions, wrappers, options, config, boilerplate, or "future-proofing." Prefer deleting code over adding it. Preserve existing conventions and keep values in their native form.

Never cut validation at trust boundaries, data-loss prevention, security, accessibility, or anything explicitly requested. For non-trivial logic (branches, loops, parsers, money, security), add one small test.

Mark deliberate shortcuts as:

`// shortcut: <limit>, <when to upgrade>`

End every response with:

- What was skipped or not checked
- Any risk the user should know

**Also:**

- **Clean as you go.** When something is replaced, delete the old code, config, secrets and files in the same change.
- **Platform over code.** Don't rebuild auth, rate limiting, storage or schedulers that the platform already provides.
- **Can't change a platform setting yourself? Write the steps for the human.** Never write workaround scripts or home-made substitutes.
- **Ignore OS clutter** (`.DS_Store`, `Thumbs.db`): keep it in `.gitignore`; never read, edit, commit or mention it.

## Stack defaults

- **Web:** Next.js.
- **Native app:** Flutter.
- **Hosting:** Cloudflare (or Vercel). Manage the live environment with the platform's CLI (`wrangler`, `vercel`). **Always ask the user before deploying** or changing anything live (deploys, remote migrations, secrets, DNS).
- **Cloudflare accounts:** run cross-account commands outside the project folder with `CLOUDFLARE_ACCOUNT_ID`. Moving a project to another account needs the user's confirmation and a full move: D1/R2/KV, secrets, Access, URLs, then deleting the old resources.
- **AI:** the provider's official SDK and its current best model. For agents, prefer hosted agent infrastructure and skills over hand-written agent loops.
- **Tests:** the runtime's built-in test runner. No framework unless it's needed.

## Local first, deploy on request

Every project starts local: get it running on this machine fast, with no hosting, deploys or cloud resources. It goes live only when the user says "deploy"; then you do the whole deploy.

**Local:**

- **Build on the production stack from day one,** in its local mode (e.g. `wrangler dev` with local bindings), so deploying is configuration, not a port.
- One command each: setup (local data and demo data), dev, test, deploy.
- Ship realistic **demo data** with relative dates, so the app can always be evaluated.
- Schema changes are numbered migrations: apply locally first, remotely only at deploy.

**First deploy:** the user saying "deploy" approves every step below (never anything destructive), so don't ask again step by step. Batch what only the human can do (login, secret values, dashboard limits, plan upgrades) into as few messages as possible, with exact commands or clicks.

1. **Login:** the platform CLI is installed and logged in (`wrangler whoami`, `vercel whoami`). On Cloudflare, `account_id` is pinned in the wrangler config (if `wrangler whoami` lists several accounts, ask which); never deploy without it.
2. **Resources:** create with the CLI whatever the app uses locally (project, database, storage, queues, crons) and wire it into config. On a new Cloudflare account, the human first enables R2 and Zero Trust in the dashboard.
3. **Lock it down:** apply Auth & secrets; if `docs/BRIEF.md` says private, it's behind access control from the first deploy.
4. **Secrets:** into the platform's secret store. Generate random ones straight into it; for keys the user holds, give them the command. Until Access is on, a new deploy is public: set no paid secrets (API keys) before then.
5. **Cost:** nothing paid goes live until the dashboard ceilings in `docs/COST_CAUTIONS.md` are set and its worst case is current.
6. **Ship:** remote migrations, deploy, then check the main flow on the live URL. Use the platform's default URL; a custom domain only if asked.
7. **Record it:** fill `docs/HOSTING.md` (a link to every service and dashboard, numbered one-time human steps, exact commands), the deploy command in `AGENTS.md`, and the hard ceilings in `docs/COST_CAUTIONS.md`. Report the live URL and anything left for the human.

**Later deploys:** only when the user asks, with the commands in `docs/HOSTING.md`. Update `docs/HOSTING.md` whenever a service, URL or setup step changes.

## Auth & secrets

- Use the platform's auth (e.g. an access-control or identity product) before writing auth code.
- **One home per secret:** the hosting platform's secret store for deployed apps; a gitignored env file (e.g. `.env.local`, `.dev.vars`) for local dev, with its own low-limit dev keys; the OS keychain for local tools. Never put secrets in code, repos, prompts or chat.
- One credential per consumer (app, agent, tool), each with an expiry, so each can be revoked on its own.
- Turn off anything that bypasses auth, such as unprotected preview URLs.
- **AI agents and prompt injection:** an agent that reads untrusted input *and* has internet access *and* sees private data can leak data. Separate those capabilities across agents, and keep credentials out of the agent's reach (vaults, injected at egress, limited to the hosts that need them).

## Cost safety

**Mandatory.** Every project has a `docs/COST_CAUTIONS.md`, and every agent follows it on every change: no code may ever loop, fan out or retry its way into a large cloud or AI bill. It holds the backstory, the hard rules, the pre-change check, loop patterns and required limits, plus this project's worst case. Never delete it; "no paid usage yet" is a valid entry.

## UI standards

**Good UI speaks for itself. Think like Apple:** minimal, clean, modern, calm.

### Text: cut it, never the visuals

These rules cut **text only**. Keep or improve layout, spacing, type scale, color, cards, dividers, status indicators, icons, hover states and empty states.

Replace text with visual signals:

- Status → nothing if it isn't needed; otherwise a colored dot; a pill only when the dot alone would be ambiguous. Never a sentence. Indicators are noise unless they help a decision.
- Progress or counts → progress bar, ring or big number, not helper text
- Priority or impact → color weight or tag, not a description
- Category or market → small icon or chip, not a kicker line
- Warnings → one-line banner with an icon, not a paragraph

Text budget per page:

- 1 page title, optionally 1 short subtitle (max 8 words)
- Section titles: yes. Section descriptions: no.
- Stats: big number + 1–2 word label. No helper line.
- List items: title + max 1 line of detail.
- Any single fact appears once per screen.

### Visual hierarchy

**Mandatory.** Every screen tells the eye where to look first, second, third, through size, weight, contrast and whitespace, never through labels explaining each section.

- **One focal point per screen:** the key number, item or action is clearly the most prominent thing.
- **One primary action per view;** everything else is secondary or a text button.
- **Few type sizes:** one large title, then small steps; use weight and color for the rest.
- **Quiet the secondary, don't shout the primary:** smaller, lighter, gray (still WCAG AA contrast) instead of more bold.
- **Order and group:** most important first; related things close together, more space between groups than within; one grid.
- **Squint test:** blur the screen; the most important thing must still stand out.

### Visual style

- **System font stack;** tight letter-spacing on headings.
- **Neutral palette plus one accent.** Color carries meaning (status, priority), never decoration.
- **Generous whitespace** on a consistent spacing scale (e.g. 4/8 px); soft cards, not borders and boxes.
- **Soft depth:** rounded corners, subtle shadows, hairline dividers.
- **Recognizable visuals over words:** logos, avatars and icons help people place things instantly.
- **Quiet motion:** short, purposeful transitions (drawers, hovers); nothing decorative.
- **Every state designed:** empty, loading, error. Empty states are an icon + 2–3 words.

### Interaction

- Fast paths for power users: keyboard shortcuts, search, deep links to specific items.
- **Light mode only,** unless the user asks for dark. Mobile-safe (16px gutters, no horizontal scroll).
- Data stays live without manual refresh where it changes in the background.

## Docs every project keeps

Keep the root to what tools and people look for there (`README.md`, `AGENTS.md`, `CLAUDE.md`); every other doc goes in `docs/`. Write docs for any coding agent, not one tool.

| File | Content |
|---|---|
| `README.md` | What it is, folder layout, local and deploy commands |
| `AGENTS.md` | Project rules for coding agents; points to this file |
| `CLAUDE.md` | Imports `AGENTS.md` and the two mandatory docs, because Claude Code doesn't read `AGENTS.md` |
| `docs/BRIEF.md` | What to build, in the user's words (the starting input) |
| `docs/COST_CAUTIONS.md` | **Always.** Runaway-cost rules for agents, plus this project's limits and worst case |
| `docs/HOSTING.md` | Links, one-time setup, deploy, local; filled at the first deploy |
