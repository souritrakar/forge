# Workflow: Visual-Refinement Loop

**Type:** repeatable workflow / loop — **not** a specialized agent. It has no distinct persona; it is
a procedure a worker follows to iterate a built UI to visual fidelity.
**Runs as:** a **ship-shaped** phase after a UI build's first implementation. Either the same frontend
builder continues into this loop, or firstmate dispatches a dedicated refinement ship task that
follows it. **Separate from no-mistakes.**
**Dispatch profile hint:** UI / frontend — Claude family, effort medium–high.
**Consumes:** the **Design Brief** (from the Design Planner agent) and reference screenshots.
**Produces:** refined frontend on the ship branch + a short fidelity report.

## When to run
- A UI build is implemented and must reach visual fidelity against a Design Brief or references.
- Responsiveness, accessibility, interaction states, or "it doesn't feel right yet" need a creative
  iteration pass before delivery.

## When NOT to run
- Design is not yet decided — run the **Design Planner** agent first.
- Non-visual code review, tests, docs, lint — that is the selected delivery path (no-mistakes).

## Skills / tools / MCP
- Browser: `chrome-devtools-axi` (or `playwright` / `webapp-testing`) — run the app, screenshot, compare.
- Review/fix: `polish-pass`, `hierarchy-rhythm-review`, `interaction-states-pass`,
  `make-responsive`, `accessibility-audit`, `web-design-guidelines`, `ai-slop-check`.

## The loop
1. Run the built app in a browser (read-only run if needed).
2. Screenshot the actual UI; compare against the Design Brief and references.
3. Identify gaps: spacing, layout, hierarchy, typography, states
   (default/hover/active/disabled/focus), responsiveness across breakpoints, accessibility, and feel.
4. Fix in code; re-run; re-compare.
5. **Repeat 2–4** until the exit criteria are met.

## Exit criteria
- UI matches the Design Brief; responsive; accessible; and the captain approves the look and feel
  (surface the final result via `lavish-axi` before the work leaves this loop).

## Boundary with no-mistakes
- This loop owns visual fidelity and creative refinement. Do not offload UI/visual work to
  no-mistakes, and do not let no-mistakes be the creative refinement loop.
- After the exit criteria pass and the captain approves, the work proceeds to the selected delivery
  path (no-mistakes / direct-PR), which owns code review, tests, docs, and lint.

## Handoff
- → selected delivery path for code review and merge authority.

## Guards
- Ship-shaped: full worktree isolation and the delivery contract apply. Limit changes to visual
  fidelity and its direct requirements. Does not replace no-mistakes code review.
- Surface captain approval through firstmate, or host the Lavish loop and stay alive.
