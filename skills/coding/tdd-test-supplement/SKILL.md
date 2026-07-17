---
name: tdd-test-supplement
description: >-
  Review and supplement tests after a TDD implementation slice, especially from
  issue-driven-dev. Use after a RED-to-GREEN-to-REFACTOR cycle when Codex should add only
  focused Vitest unit/component tests and a small number of contract tests, and
  recommend where those tests should live.
---

# TDD Test Supplement

After a TDD implementation is green, audit whether the test set proves the issue
acceptance criteria without drifting into slow workflow or E2E coverage.

This skill does not replace [../tdd/SKILL.md](../tdd/SKILL.md), does not choose
the first RED test, and does not discover regression commands. Use
[../issue-driven-dev/regression.md](../issue-driven-dev/regression.md) for the
PR-before-merge gate.

## Scope

Add or recommend only:

- **Vitest unit tests** for public domain logic, validation, state transitions,
  formatting, permission calculations, and pure helpers.
- **Vitest component tests** for rendered UI behavior, form interaction,
  loading/error/empty states, accessibility-relevant labels, and local state.
- **Small contract tests** for API handlers, data access adapters,
  authorization checks, webhook parsing, file/storage boundaries, or service
  wrappers when the changed behavior depends on the boundary shape.

Do not add Playwright, full journey, broad browser visual, or long-running
integration tests here. If E2E coverage is warranted, propose a separate issue
using [../e2e-test-issue/SKILL.md](../e2e-test-issue/SKILL.md).

## Inputs

Use existing context in this order:

1. Issue acceptance criteria and PR diff.
2. Tests already written during TDD.
3. Nearby test naming, helpers, fixtures, and setup files.
4. Public interfaces used by production callers.

In AFK `issue-driven-dev`, do not ask the user to approve the supplement plan.
Make a conservative call and continue.

## Review Pass

Check the TDD tests against these questions:

- Does each acceptance criterion have at least one fast, stable proof?
- Are edge cases missing only where they are likely to regress?
- Are mocks limited to system boundaries such as time, randomness, network,
  email, payments, file system, storage, or unavailable infrastructure?
- Are tests named in domain language rather than implementation details?
- Are failures easy to diagnose from the test name and assertion?

Prefer no new test when the existing TDD test already proves the behavior.

## Placement Guidance

Recommend or use the closest existing convention:

- Unit tests live next to the public module or in the repo's established
  `__tests__`, `*.test.ts`, or `*.spec.ts` pattern.
- Component tests live next to the component or in the established component
  test directory, using the repo's render helper.
- Contract tests live near the boundary adapter/handler or in the existing
  API/contract test area.

When adding tests, name the file after the public behavior surface, not the
private helper introduced to implement it.

## Output

Report briefly:

- Added tests, grouped by unit/component/contract.
- Suggested test locations when tests are only recommended.
- Any acceptance criterion intentionally left to E2E and why.

## Anti-Patterns

- Rewriting the TDD test suite from scratch after implementation.
- Adding Playwright or journey coverage in this skill.
- Chasing coverage percentage.
- Mocking internal modules to force isolation.
- Adding broad contract tests for every service call.
- Creating a test-only issue unless the missing confidence belongs in E2E or
  test infrastructure.
