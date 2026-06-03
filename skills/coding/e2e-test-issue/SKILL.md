---
name: e2e-test-issue
description: >-
  Create standalone Playwright E2E testing issues. Use when planning browser
  coverage separately from implementation work: start with necessary small,
  stable cases that run frequently with traces, then compose those cases into
  larger journeys only where the product path needs end-to-end confidence.
---

# E2E Test Issue

Write issue guidance for Playwright E2E coverage that is intentionally separate
from feature implementation and TDD supplement work.

Use this when the desired outcome is an issue or issue plan, not immediate
implementation inside an existing feature PR.

## Principles

1. Start with the necessary small cases.
2. Keep each case stable, independent, and cheap enough to run often.
3. Enable Playwright trace collection for debugging failures.
4. Compose proven small cases into larger journeys only for critical product
   promises that need cross-feature confidence.

## Small Case Shape

A small E2E case should:

- Cover one recognizable user behavior or externally visible workflow step.
- Own its setup data or use stable seeded fixtures.
- Avoid dependence on test order, wall-clock timing, remote third-party state,
  email inboxes, payments, or external services unless the boundary is mocked or
  explicitly part of the test environment.
- Assert domain-visible outcomes, URLs, persisted state, or accessible UI state.
- Capture traces on retry or failure using the repo's Playwright config.

Prefer several small tests over one large journey when failures would otherwise
be hard to diagnose.

## Journey Composition

Create or update a journey only when:

- The product promise spans multiple capabilities.
- Unit/component/contract tests cannot prove the integration risk.
- The smaller cases already cover the individual steps.
- The journey can stay deterministic with controlled data and bounded runtime.

Journeys should be smoke tests: one happy path or one high-value failure path,
not a matrix of every branch.

## Issue Template

When creating the issue, include:

- **Goal**: the product confidence this E2E coverage should provide.
- **Small Playwright cases**: each case title, setup, actions, assertions, and
  trace expectation.
- **Journey composition**: optional larger journey and which small cases it
  depends on.
- **Stability constraints**: data seeding, mocks, retries, timeouts, selectors,
  and external-service handling.
- **Run profile**: which cases run frequently and which journeys run less often.
- **Acceptance criteria**: concrete test files or specs added, traces available
  on failure, and documented run command.

## Placement Guidance

Recommend the repo's existing Playwright layout first:

- Add specs beside related E2E specs when a feature folder exists.
- Put shared setup in existing fixture/helper files.
- Keep journey specs separate from small case specs when the repo already has a
  smoke/journey distinction.

If the repo has no convention, propose:

- `tests/e2e/<feature>.spec.ts` for small cases.
- `tests/e2e/journeys/<journey>.spec.ts` for composed journeys.

## Anti-Patterns

- Adding E2E coverage to every implementation issue by default.
- Starting with one large journey before stable small cases exist.
- Testing implementation details, CSS classes, or private component structure.
- Depending on fragile sleeps instead of locator state and domain outcomes.
- Letting trace collection be optional for new E2E coverage.
- Mixing broad E2E work into the TDD supplement pass.
