# forge

Run a crew of coding agents from one place.

You talk to one agent, the first mate. It spawns worker agents into their own terminal windows and clean git worktrees, watches them while they work, and hands back finished pull requests or investigation reports. You stay the captain. You never babysit five terminals at once.

forge is my setup on top of [firstmate](https://github.com/kunchenguid/firstmate): the same orchestration core, plus the roles, workflows, model routing, and guidelines I use day to day. If you already run firstmate, forge is a way to pick up a working config instead of building one from scratch.

## What it's good for

- **Parallel work.** Several isolated tasks at once, each in its own worktree, no stepping on each other.
- **Cross-model builds.** One model family implements, the opposite one reviews. You get diversity and a second opinion by default.
- **Shipping real PRs.** Work goes through review, tests, lint, and CI before it reaches a branch you care about.
- **Investigations.** Point a scout at a question and get a written report back, no code changes.

## How it fits together

| Piece | Job |
| --- | --- |
| **firstmate** | The brain. You talk to it, it dispatches and supervises workers. |
| **Treehouse** | Worktree isolation. Every task gets a clean, disposable copy of the repo. |
| **Herdr** | The terminal backend. Each worker runs in its own pane you can watch. |
| **no-mistakes** | The quality gate. Review, tests, docs, lint, then push and open the PR. |

Workers run as real coding-agent CLIs (Claude, Codex, Grok, and others) in those panes. firstmate reads their state, answers their questions, and merges when the work is green.

## Quickstart

```sh
git clone https://github.com/souritrakar/forge
cd forge
```

1. Install the toolchain: `tmux`, `git`, `gh`, Node 20+, and the agent CLIs you want to run (Claude, Codex, Grok). See [docs/forge/setup.md](docs/forge/setup.md).
2. Install the axi tools forge leans on (`gh-axi`, `lavish-axi`, `quota-axi`, and friends). Setup doc has the list.
3. Start the first mate: `cd forge && claude` (or whichever agent you run as the brain).
4. Talk to it in plain language. "Add X to project Y", "look into why Z is slow". It takes it from there.

Full walkthrough: [docs/forge/setup.md](docs/forge/setup.md).

## What's inside

- **The firstmate core** — the operating contract (`AGENTS.md`), the helper scripts (`bin/`), and the skills the first mate loads (`.agents/skills/`).
- **The custom layer** — the reason forge exists:
  - [`forge/roles/`](forge/roles/) — specialized agent roles (Context Scout, Research Scout, Planner, Design Planner). Reusable blueprints the first mate drops into a task instead of re-explaining the same job every time.
  - [`forge/workflows/`](forge/workflows/) — repeatable loops a worker follows, like the visual-refinement loop for frontend work.
- **Model routing** — how work maps to models, in [docs/forge/models.md](docs/forge/models.md).

## The docs

- [docs/forge/setup.md](docs/forge/setup.md) — install and run it.
- [docs/forge/models.md](docs/forge/models.md) — model usage, routing, and effort.
- [forge/roles/README.md](forge/roles/README.md) — the roles and when to use each.
- [forge/workflows/](forge/workflows/) — the workflow loops.
- [AGENTS.md](AGENTS.md) — the full first-mate operating contract. Dense, but it is the source of truth.

## Credit

forge is a fork of [firstmate](https://github.com/kunchenguid/firstmate) by Kun Chen. The orchestration core is his. The roles, workflows, routing, and guidelines here are mine, and they are the part worth borrowing.
