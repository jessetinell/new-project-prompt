# New Project Prompt: maintainer guide

You are working **on New Project Prompt itself**, not on a project made from it.

## What this repo is

- `template/`: what users get. Its **contents** are copied into new, empty project folders, where agents set up a real project by following `template/START_HERE.md`.
- Root files (`README.md`, `CONTRIBUTING.md`, `LICENSE`, this file, `CLAUDE.md`): about this repo. They are **never copied**.

Don't mix the two: `template/AGENTS.md` and `template/CLAUDE.md` belong to future projects and have nothing to do with this file.

## Files in `template/`

| File | Role in a new project |
|---|---|
| `START_HERE.md` | Bootstrap playbook for the agent; deleted once the project is set up |
| `AGENTS.md` | The project's agent guide (cross-tool standard) |
| `CLAUDE.md` | Shim for Claude Code: imports `AGENTS.md`, `docs/STANDARDS.md` and `docs/COST_CAUTIONS.md` |
| `README.md` | Template the agent fills at setup |
| `docs/BRIEF.md` | What to build; the agent writes it from the user's idea |
| `docs/STANDARDS.md` | Shared philosophy, stack, deploy, auth, UI and docs rules; identical in every project |
| `docs/COST_CAUTIONS.md` | Mandatory runaway-cost rules (fixed part) + project limits (placeholder part); never deleted |
| `docs/HOSTING.md` | Template the agent fills at the first deploy |

## Rules for changes

- **Markdown only.** No code, scripts or config in this repo beyond `LICENSE`, `.gitattributes` and `.gitignore` (shell one-liners inside docs are fine).
- **Everything in `template/` stays generic:** no project names, URLs, IDs or account details. Use `{{placeholders}}` for anything project-specific. In a fork, links every project shares (e.g. a team dashboard) are fine.
- **Tool-neutral:** write for any coding agent (Codex, Claude Code, others). `CLAUDE.md` is the only tool-specific file; no tool folders like `.claude/`.
- **Minimal project root:** only `README.md`, `AGENTS.md`, `CLAUDE.md` and `START_HERE.md`; every other template doc goes in `template/docs/`.
- **Never weaken** the cost-safety or security rules. Add to them, clarify them, keep them brief.
- **Brief beats complete.** Every line must change what an agent does; cut anything that doesn't.
- **Keep the flow in sync:** the prompts in `README.md` and the steps in `template/START_HERE.md` must agree.
- **The philosophy applies here too:** read `template/docs/STANDARDS.md` before editing.
- **Ignore OS clutter** (`.DS_Store`, `Thumbs.db`) anywhere in this repo: never read, edit, commit or mention it.
- **Where improvements come from:** lessons from real projects. Generalize them (strip specifics), then put each in the one file it belongs to; don't repeat a rule across files.

## Check a change

1. Bootstrap into a scratch folder: `d=$(mktemp -d) && cp -R template/. "$d" && ls "$d"`. You should see only template files.
2. Read `template/START_HERE.md` as a fresh agent would, with no other context: can it set up a project from just a one-line idea?
3. `grep -rn "{{" template/` shows only intended placeholders.
