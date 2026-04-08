# gstack for OpenAI Codex CLI

gstack is a set of structured “specialist” workflows (product, engineering, design, QA, security, release) packaged as Markdown skills.

If you like the “agent in your terminal” vibe of Claude Code, OpenAI’s Codex CLI can run the same gstack workflow. This doc shows how to install Codex CLI and wire gstack into it.

## Who this is for

- You want an agentic coding loop in the terminal, but you prefer Codex CLI.
- You want repeatable slash-command workflows instead of “blank prompt and vibes”.
- You want QA and browser-driven debugging, not just codegen.

## Quick start

1. Install Codex CLI
2. Sign in
3. Install gstack for Codex
4. Open any repo and run gstack skills

## Requirements

- Git
- Bun (setup builds the `browse` binary)
- Node.js (Windows only)

## 1) Install Codex CLI

Install globally with your preferred package manager:

```bash
# npm
npm install -g @openai/codex

# Homebrew (macOS)
brew install --cask codex
```

Then authenticate:

```bash
codex
```

Choose “Sign in with ChatGPT” in the UI, or use `codex login` if you prefer a direct command.

## 2) Install gstack skills for Codex

gstack installs Codex-compatible skill bundles into your Codex skills directory (default: `~/.codex/skills/`).

```bash
git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git ~/gstack
cd ~/gstack
./setup --host codex
```

What setup does:

- Builds the `browse` binary (Playwright Chromium automation) so QA and browser skills work
- Generates Codex-format skill definitions under `.agents/`
- Symlinks gstack skills into `~/.codex/skills/`

## 3) Use gstack inside Codex

In any git repo:

```bash
cd /path/to/your-repo
codex
```

In the Codex session, use `$` to select a gstack skill agent.

In practice, you typically type `$gstack-`, pick from the autocomplete list, and press Enter to insert it.

Examples:

```text
$gstack-office-hours
```

Then describe what you want in plain English and follow the prompts.

Common picks:

- `$gstack-office-hours` for product thinking and design docs
- `$gstack-plan-ceo-review` to challenge scope and strategy
- `$gstack-plan-eng-review` to lock architecture and tests
- `$gstack-review` for code review on your current changes
- `$gstack-qa` for browser-driven QA and bug fixing
- `$gstack-ship` to run tests and open a PR

The full catalog is in [docs/skills.md](docs/skills.md).

## How the workflow maps to a sprint

Think → Plan → Build → Review → Test → Ship → Reflect

The point is that each phase leaves artifacts the next phase reads, so you get fewer dropped details and less “agent amnesia”.

## Optional: repo-local install (team sharing)

If you want gstack to live inside a repo (so teammates get the same skills without a separate global install), install it under `.agents/skills/`:

```bash
cd /path/to/your-repo
mkdir -p .agents/skills
git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git .agents/skills/gstack
cd .agents/skills/gstack
./setup --host codex
```

This keeps Codex-discoverable skill artifacts colocated with the repository, while still using symlinks for runtime assets.

## Troubleshooting

- Skills not showing up: rerun `./setup --host codex` from your gstack checkout
- Browse-related failures: rerun `bun install && bun run build` from the gstack checkout
- “Skipped loading skill(s) due to invalid SKILL.md”: your generated skill docs are stale, update the gstack checkout and rerun setup

## Notes on “/codex”

gstack also includes a `/codex` skill, but it’s meant for other hosts (like Claude Code) to call Codex CLI as a second opinion. When you are already running inside Codex CLI, you typically use the built-in `codex` commands directly instead.
