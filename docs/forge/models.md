# Models and routing

forge sends different kinds of work to different models on purpose. The goal is simple: use the model that is good at the job, and get a second opinion from a different family.

## The idea

- **Planning, design, diagnosis** go to strong reasoning models (the Claude family works well here).
- **Implementation and testing** go to fast, capable coding models (Codex and Grok families).
- **Review is cross-model.** Whatever family writes the code, the opposite family reviews it. One implements, the other checks. You catch more this way than running one model against itself.

You do not route by hand. The first mate reads a config file and picks per task.

## The config

Routing lives in `config/crew-dispatch.json`. Start from the example:

```sh
cp forge/config/crew-dispatch.example.json config/crew-dispatch.json
```

Each rule has three parts:

- `when` — a plain-language description of the kind of work.
- `use` — one model, or a list of candidates the first mate chooses between.
- `why` — a note to your future self about the rule.

When a rule lists several candidates, the first mate reads live quota (via `quota-axi`) and picks one with headroom, so a busy or empty model does not block work.

## Effort

Each model runs at a reasoning effort level:

- **low** — well-understood, explicit work.
- **medium** — normal features and fixes.
- **high / xhigh** — ambiguous investigation or design.

Set a default in the config, override per task when you need to. Do not reach for the highest effort by default. Match it to how much thinking the task actually needs.

## Making it yours

The model names in the example are just names. Edit them to match what you have access to, and check them against each CLI's own model list before you rely on a tier. A quick way: run the CLI's `models` command and confirm the ids exist.

## Quota

`quota-axi` reports what each provider has left. The first mate uses it to route around an exhausted model. If everything you route to is empty, work parks and waits for a reset rather than failing. Keep at least two families available so one running dry does not stop you.
