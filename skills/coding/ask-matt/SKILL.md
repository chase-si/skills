---
name: ask-matt
description: Recommend the smallest skill or workflow that fits the user's current situation. Use when the user is unsure which installed skill to invoke, asks where to start, or wants a route from an idea, bug, issue queue, review, architecture concern, frontend task, or skill-authoring task to completion.
---

# Ask Matt

Route the user to the smallest workflow that fits their situation. Recommend a
path; do not start it until the user explicitly asks.

## Routing Rules

1. Identify the user's current state: idea, known issue, bug, implementation,
   review, architecture concern, frontend work, E2E planning, session transfer,
   or skill authoring.
2. Recommend one primary path. Add one conditional branch only when it would
   materially change the next step.
3. Explain why the first skill fits in one sentence and give the exact Codex
   skill invocation to use next.
4. Avoid dumping the full catalog unless the user explicitly asks for it.

## Main Flow: Idea to Ship

- **Idea in an existing codebase** → `$grill-with-docs` to resolve decisions
  while keeping `CONTEXT.md` and ADRs current.
- **Plan or design without a codebase** → `$grill-me` for a stateless interview.
- **A design question needs something runnable or visible** → detour through
  `$prototype`, then return with the answer.
- **Multi-issue or multi-session build** → `$to-prd` → `$to-issues` →
  `$issue-driven-dev`. The final skill runs each confirmed issue through TDD,
  focused test supplementation, regression checks, two-axis review, PR, and
  merge policy.
- **One concrete behavior with no issue/PR orchestration needed** → `$tdd`.
  Follow with `$tdd-test-supplement` when the green slice needs a focused test
  audit, then `$code-review` when a fixed-point review is useful.

For frontend implementation inside either route, apply `$frontend-ui` alongside
the implementation skill so layout, responsive behavior, states, and browser
rendering receive explicit attention.

## Starting Situations

- **Something is broken, flaky, failing, or slow** → `$diagnosing-bugs`.
  Establish a tight red-capable feedback loop before theorizing. Use `$tdd` for
  the regression fix; if the post-mortem finds no good test seam, continue with
  `$improve-codebase-architecture`.
- **A confirmed ordered issue queue must be delivered through PRs** →
  `$issue-driven-dev` directly. Do not send already-approved issues back through
  `$to-prd` or `$to-issues`.
- **A branch, PR, or worktree needs review** → `$code-review`, with the fixed
  point and originating issue or PRD when available.
- **The codebase is hard to change or navigate** →
  `$improve-codebase-architecture`. A selected candidate becomes an idea that
  can re-enter the main flow at `$grill-with-docs`.
- **Playwright coverage should be planned separately** → `$e2e-test-issue`, then
  place the approved E2E issue into `$issue-driven-dev` like any other issue.
- **A frontend-only change is already clear** → `$frontend-ui`; add `$tdd` when
  the change includes behavior worth proving test-first.

## Context and Skill Maintenance

- **Engineering skills lack tracker, triage, or domain-doc context** →
  `$setup-matt-pocock-skills` before the rest of the engineering flow.
- **The current conversation must continue in a fresh session** → `$handoff`.
- **Create a new reusable skill** → `$write-a-skill`, then audit the draft with
  `$writing-great-skills`.
- **Review or refine an existing skill** → `$writing-great-skills` directly.

## Output Contract

Return:

- **Recommended path:** the ordered skill sequence.
- **Why:** one concise reason tied to the user's current state.
- **Next invocation:** the exact first skill prompt to run.
