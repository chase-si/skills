---
name: issue-driven-dev
description: >-
  Implements a single issue on a dedicated feature branch using TDD, runs
  project regression checks, opens a PR that closes the issue against a recorded
  parent branch, and babysits until merge-ready. Use when the user wants
  issue-driven development, a feature-branch workflow, to implement issue #N,
  or to standardize branch → TDD → PR flow.
disable-model-invocation: true
---

# Issue-Driven Development

One issue, one **feature branch**, TDD implementation, regression gate, PR that closes the issue, then babysit until **merge-ready**. Merge and branch cleanup happen only when the user explicitly asks in the same session.

## Terminology

| Term | Meaning |
|------|---------|
| **parent-branch** | Branch checked out when this workflow starts; PR base for the whole run |
| **feature branch** | New branch scoped to a single issue |
| **regression gate** | Required local test/build (and lint if CI runs it) before opening a PR |
| **merge-ready** | PR is green, conflicts resolved, review comments triaged |

## Quick start

User invokes with an **issue reference** (number, URL, or local path). Optionally override **parent-branch**; otherwise record `git branch --show-current` before branching.

## Workflow checklist

```
- [ ] Phase 0: Preconditions (issue ref, slice type and blockers checked, parent-branch recorded, tree OK to branch)
- [ ] Phase 1: Sync parent-branch (fetch + pull), then create feature branch
- [ ] Phase 2: Implement with /tdd
- [ ] Phase 3: Regression gate (all green)
- [ ] Phase 4: Open PR (base = parent-branch, Closes issue) → babysit to merge-ready
- [ ] Phase 5 (optional): Merge + delete feature branch — only if user explicitly asks
```

## Phase 0 — Preconditions

- Resolve the **issue** (title, body, acceptance criteria).
- Read the issue body for `/to-issues` metadata:
  - **Type**: If the issue is marked **HITL**, stop and ask the user before executing. Only proceed automatically for **AFK** issues or when the user explicitly approves a HITL issue.
  - **Blocked by**: If any blocker is listed, verify it is complete/closed before branching. If a blocker is incomplete or unclear, stop and report the blocking issue instead of implementing.
- Record **parent-branch** with `git branch --show-current`. Do not change PR base mid-flow unless the user says so.
- Warn on a dirty working tree; do not stash unless the user asks.
- One issue per feature branch — do not batch multiple issues.

## Phase 1 — Branch from issue

1. Derive the branch name: `<issue-number>-<kebab-slug-from-issue-title>`  
   Example: issue `#42` titled "Add checkout validation" → `42-add-checkout-validation`  
   Slug: lowercase, hyphens, drop punctuation; truncate if the host limits branch name length.
2. Check out **parent-branch** if you are not already on it.
3. **Always** sync **parent-branch** to the latest remote tip before branching: `git fetch`, then `git pull` (or `git pull --ff-only` when the repo expects fast-forward only). Resolve pull failures with the user; do not create the feature branch on a stale **parent-branch**.
4. From updated **parent-branch**, run `git checkout -b <feature-branch>`.

## Phase 2 — Implement with TDD

- Read and follow [../tdd/SKILL.md](../tdd/SKILL.md) for the entire implementation (vertical RED→GREEN slices; never horizontal “all tests then all code”).
- Scope to issue acceptance criteria; use project domain language from `CONTEXT.md` when present.
- Commits and pushes only when user rules allow (default: user must ask to commit).

## Phase 3 — Regression gate

Do **not** open a PR until the regression gate passes on the **feature branch**.

Discover commands using [regression.md](regression.md). Run the minimal set that matches CI for this repo (tests, build, lint if CI runs lint). If ambiguous, ask the user once and reuse that choice in the session.

On failure: fix in Phase 2, re-run until green.

## Phase 4 — Pull request and babysit

Follow the user’s **creating-pull-requests** rule: parallel `git status` / `git diff` / log vs base, push with `-u`, `gh pr create` with a HEREDOC body.

| Field | Rule |
|-------|------|
| Base | **parent-branch** (not assumed `main`) |
| Title | Concise; tied to the issue |
| Body | Summary, test plan checklist, **`Closes #<number>`** (or tracker equivalent) |

Then follow the **babysit** skill: triage comments, resolve conflicts, fix in-scope CI until **merge-ready**.

**Merge-ready stop condition:** stop babysitting when:

- CI is green.
- There are no merge conflicts.
- There are no unresolved review comments.
- The PR body includes the correct closing keyword for the issue.
- The branch is up to date when the repo requires it.

**Default stop:** merge-ready PR. Do **not** merge or delete branches unless Phase 5 is explicitly requested.

## Blockers

Stop and ask the user when:

- Issue requirements conflict or acceptance criteria are unclear.
- The issue is marked **HITL** and the user has not explicitly approved execution.
- The issue has an incomplete or unclear **Blocked by** dependency.
- **parent-branch** cannot be pulled cleanly.
- Tests require missing secrets, services, or unavailable infrastructure.
- CI failure appears unrelated to the issue or outside scope.
- Implementation would require expanding beyond one issue.
- Babysit has been attempted for two rounds and the PR is still not merge-ready.

## Phase 5 — Merge and cleanup (explicit only)

When the user asks to merge and clean up (same session):

1. `gh pr merge` — prefer the repo’s usual method (inspect recent merged PRs or ask once).
2. Checkout **parent-branch**, `git pull`.
3. Delete **feature branch**: local `git branch -d <feature-branch>`; remote `git push origin --delete <feature-branch>`.
4. Confirm the issue closed (e.g. GitHub auto-close from `Closes #N`).

Git safety: no force-push to **parent-branch**; no `git branch -D` unless merge failed and the user insists.

## Output expectations

At each phase transition, briefly report:

- Current branch.
- **parent-branch**.
- Issue title.
- Next action.
- Any user decision needed.

Final response should include:

- PR URL.
- Regression commands run.
- Merge-ready status.
- Any residual risk.

## Examples

**Branch name**

| Issue | Feature branch |
|-------|----------------|
| `#7` — "Fix null cart total" | `7-fix-null-cart-total` |

**PR body snippet**

```markdown
## Summary
- …

## Test plan
- [ ] …

Closes #42
```

## Anti-patterns

- Implementing on **parent-branch** instead of a feature branch
- Skipping the regression gate before opening a PR
- Horizontal TDD (all tests upfront, then all code)
- Retargeting PR base away from recorded **parent-branch** without user approval
- Merging or deleting the feature branch without explicit user request

## Related skills

- [../tdd/SKILL.md](../tdd/SKILL.md) — implementation loop
- [regression.md](regression.md) — discover test/build commands

- **babysit** — merge-ready PR loop
