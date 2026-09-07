# Setup

What you need to install, and how to start forge.

## Prerequisites

- **tmux** (3.x) — forge runs each worker in its own pane. Required.
- **git** and **gh** (GitHub CLI, logged in) — for worktrees and PRs.
- **Node 20+**.
- **Coding-agent CLIs** — install the ones you want to run as workers. At least one:
  - Claude Code (`claude`)
  - Codex (`codex`)
  - Grok (`grok`)
- **Treehouse** — worktree isolation. Workers run in disposable copies of the repo.
- **Herdr** — the terminal backend forge spawns panes into.
- **no-mistakes** — the review-and-ship gate. Run `no-mistakes init` once per project.

## The axi tools

forge leans on a set of small, agent-friendly CLIs. Install the ones you use:

- `gh-axi` — GitHub for agents (issues, PRs, runs). `npx -y gh-axi`
- `quota-axi` — reports model quota so routing knows what has headroom.
- `lavish-axi` — turns agent-generated HTML into a review surface for you.
- `chrome-devtools-axi` — browser control for anything that needs a real page.
- `tasks-axi` — the backlog backend.

There is a wider catalog at [axi.md](https://axi.md) if you want more (Linear, Notion, Cloudflare, and so on). Add only what your projects touch.

## First run

```sh
git clone https://github.com/souritrakar/forge
cd forge
```

1. Copy the routing config into place:
   ```sh
   mkdir -p config
   cp forge/config/crew-dispatch.example.json config/crew-dispatch.json
   ```
   Edit it so the model names match what you have access to. See [models.md](models.md).
2. Copy the roles and workflows into your working home if you want them active:
   ```sh
   mkdir -p data/roles data/workflows
   cp forge/roles/*.md data/roles/
   cp forge/workflows/*.md data/workflows/
   ```
3. Start the first mate from the forge directory:
   ```sh
   claude
   ```
   (or `codex`, or whichever agent you run as the brain.)
4. Talk to it. Add a project, give it a task, and it dispatches a worker.

## How your projects plug in

forge is read-only over your projects. It clones them under `projects/` and workers change them in isolated worktrees. Your real repos are never touched directly. Ask the first mate to add a project and it walks you through cloning and picking a delivery mode.

## What stays private

`config/`, `data/`, `state/`, `projects/`, and `.env` are local and gitignored. Your captain preferences, backlog, credentials, and cloned projects never get committed. Only the shared setup does.
