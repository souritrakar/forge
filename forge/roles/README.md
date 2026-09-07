# Specialized agent roles (this home's custom setup)

Reusable **role blueprints** firstmate composes into task briefs. A role is written once and
reused; a `brief.md` is the throwaway per-task handoff. At dispatch, firstmate points a brief's
`{TASK}` at the matching role file (a pointer, not a copy), so the worker inherits the brief's
safety chassis (worktree isolation, status protocol, definition of done) and the role's purpose,
flow, and skill loadout.

These roles are **private to this home** (gitignored under `data/`). They are the captain's custom
layer, not vanilla firstmate. When a role is stable, firstmate can promote it into the shared
`.agents/` template so it propagates to secondmates and other homes.

> **Governing principle:** use a specialized role only when the request actually fits it — the right
> thing at the right time, not a role for every request. A one-off still belongs in an ordinary
> brief. See `data/captain.md`.

## Agents vs workflows (two different primitives)

- **Specialized agents** (this folder, `data/roles/`) = roles that **reason and produce an artifact**.
  They have a purpose/persona. A worker *is* the agent for one task.
- **Workflows** (`data/workflows/`) = repeatable **loops/procedures** a worker *follows*. No persona.
  Example: the **Visual-Refinement Loop** (`data/workflows/visual-refinement.md`) — a
  run→screenshot→compare→fix loop a frontend build (or a dedicated refinement ship pass) follows
  after implementation.

Both are composed into a brief by pointer; the difference is *who* (agent) vs *how/loop* (workflow).

## The agent roster

| Role | File | Task shape | Produces | Dispatch profile (crew-dispatch) |
|---|---|---|---|---|
| Context Scout | `context-scout.md` | scout | Context Brief (codebase map) | planning / diagnosis (Claude) |
| Research Scout | `research-scout.md` | scout | Research Brief (external, sourced) | planning / diagnosis (Claude) |
| Planner | `planner.md` | scout | canonical SPEC + decomposition | planning (Claude, high) |
| Design Planner | `design-planner.md` | scout | Design Brief (+ mockups) | UI / creative design (Claude) |

## How firstmate invokes a role

1. Classify the request at intake (`AGENTS.md` §7) and resolve the project.
2. If the request matches a role's **When to use**, pick that role. Otherwise use an ordinary brief.
3. Scaffold the matching task shape with `bin/fm-brief.sh` (scout for the four thinking roles, ship
   for visual-refinement).
4. Fill `{TASK}` with the concrete task, acceptance criteria, context, **and a pointer** to the
   role file: `Follow data/roles/<role>.md for your working flow and skills.`
   Attach the upstream artifact paths the role consumes (Context Brief, Research Brief, SPEC,
   Design Brief) when they exist.
5. Resolve the dispatch profile from `config/crew-dispatch.json` per the role's hint.
6. Spawn with `bin/fm-spawn.sh`. Run several of the same role in parallel when the work is isolated.

## How the roles chain (the flow)

```
                 ┌────────────────┐      ┌────────────────┐
 request ──▶     │ Context Scout  │      │ Research Scout │     (either/both, as needed)
                 └───────┬────────┘      └───────┬────────┘
                         │  Context Brief         │  Research Brief
                         └───────────┬────────────┘
                                     ▼
                              ┌────────────┐         ┌─────────────────┐
                              │  Planner   │◀────────│  Design Planner │  (UI work)
                              │ SPEC+lanes │         │  Design Brief   │
                              └─────┬──────┘         └────────┬────────┘
                    approved SPEC + scoped task               │ Design Brief
                                    ▼                         ▼
                              ┌────────────┐          ┌───────────────┐
                              │  Builders  │  ──UI──▶  │ Visual-Refine │
                              │ (ship)     │          │ loop (ship)   │
                              └─────┬──────┘          └───────┬───────┘
                                    └──────────┬──────────────┘
                                               ▼
                              selected delivery path (no-mistakes / direct-PR)
                                               ▼
                                        captain approval / merge
```

- **Context / Research Scouts** feed everything downstream; run them first when understanding is thin.
- **Planner** is the human↔execution bridge; it consumes the briefs, resolves ambiguity by grilling
  (surfaced through firstmate), and emits the canonical SPEC + a decomposition into lanes with
  dependencies and parallelism. Builders receive the **approved SPEC + relevant Context Brief + their
  scoped task only — never the planner transcript.**
- **Design Planner** owns creative/UI reasoning and produces a builder-ready Design Brief.
- Builders are ordinary ship tasks (no special role) driven by the SPEC / Design Brief.
- **Visual-Refinement Loop** runs after a UI build to reach visual fidelity, then hands off to the
  selected delivery path. It is separate from no-mistakes; no-mistakes owns code review, not visuals.

## Surfacing & isolation (applies to every role)

- A worker never addresses the captain (hard rule 4). Open questions surface as `needs-decision:`
  status lines; firstmate relays them to the captain (plain chat or `lavish-axi`) and steers the
  answer back with `bin/fm-send.sh --resolve-key`.
- For high-bandwidth interactive grilling, firstmate may run the grill itself (it is the one that can
  talk to the captain), then hand the resolved understanding to the planner to finalize the SPEC.
- Keep each role's transcript isolated from builders. Pass artifacts (briefs, SPEC), not transcripts.
- A scout role whose deliverable is a visual artifact may host its own `lavish-axi` review loop and
  stay alive, rather than round-tripping every revision through firstmate.
