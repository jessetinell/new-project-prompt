# Start here

You are in a new project folder, copied from the `template/` folder of New Project Prompt. Follow these steps to set the project up, then delete this file.

**Local first.** Get the app running on this machine fast. No deploys, hosting logins or cloud resources now: the project goes live only when the user says "deploy", following `docs/STANDARDS.md` → Local first, deploy on request.

## Setup steps

1. **Check the copy:** this folder should contain the files listed below, not that repo's own root files (`README.md` here starts with `# {{project name}}`; there is no `LICENSE` or `template/`). Run `git init` if it isn't a repo yet.
2. **Read** `docs/STANDARDS.md` and `docs/COST_CAUTIONS.md` fully.
3. **Write `docs/BRIEF.md`** from the user's idea. Keep their words; leave unknown sections out rather than guessing.
4. **Ask only what's blocking** (at most a few questions, in one go; hosting questions wait until deploy). If it's hosted on Cloudflare, run `wrangler whoami`; if it lists more than one account, also ask which one it deploys to. Use the defaults in `docs/STANDARDS.md` for everything else.
5. **Fill the placeholders** (`{{…}}`) in `AGENTS.md`, `README.md` and the project section of `docs/COST_CAUTIONS.md`; write "not deployed yet" for anything that only exists once hosted. Leave `docs/HOSTING.md` for the first deploy. Never delete `docs/COST_CAUTIONS.md`.
6. **Scaffold the app** with the default stack (or the user's override), smallest working version first, and run it locally. On Cloudflare, pin that `account_id` in the wrangler config with a comment naming the account (not logged in yet: leave it for the first deploy).
7. **Check:** `grep -ln "{{" *.md docs/*.md` lists only this file and `docs/HOSTING.md`; the app runs locally with the dev command.
8. **Delete this file.** Then tell the user how to run it, anything they must do first (e.g. create an AI key with a spend limit), what's next, and that saying "deploy" takes it live.

## What's here

| File | Purpose | Edit? |
|---|---|---|
| `AGENTS.md` | Project rules for coding agents; the cross-tool standard most agents read automatically | Agent fills, then grows with the project |
| `CLAUDE.md` | Shim: Claude Code doesn't read `AGENTS.md`, so this imports it plus the two mandatory docs | No |
| `README.md` | Project readme | Agent fills |
| `docs/BRIEF.md` | What to build | Agent writes from the user's idea |
| `docs/STANDARDS.md` | Shared philosophy, stack, security and UI rules | Never per project; improve it upstream in New Project Prompt (or your fork) |
| `docs/COST_CAUTIONS.md` | **Mandatory.** Runaway-cost rules (fixed) + this project's limits and worst case (agent fills) | Never delete |
| `docs/HOSTING.md` | Links, one-time setup, deploy, local | Agent fills at the first deploy |
