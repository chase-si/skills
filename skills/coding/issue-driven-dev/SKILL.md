---
name: issue-driven-dev
description: >-
  Runs an ordered queue of issues serially on feature branches (TDD, regression,
  PR to a confirmed parent branch, babysit, auto-merge when AFK and merge-ready).
  Use for issue-driven development, issue queue, implementing issue #N then #M,
  按顺序实现多个 issue, or branch → TDD → PR flow. Confirms parent-branch at
  session start; reports each merge step.
disable-model-invocation: true
---

# Issue-Driven Development

Process an **issue queue** in user-specified order: for each issue, sync **parent-branch** → **feature branch** → TDD → regression gate → PR → babysit → merge (when policy allows) → refresh **parent-branch** → next issue.

**Merge policy** for the session: `auto_with_report` — when an issue is **AFK** and the PR is **merge-ready**, automatically merge, clean up the feature branch, update **parent-branch**, and report each step. Stop the entire queue on **HITL** (without approval), merge failure, or other blockers below.

## Terminology

| Term | Meaning |
|------|---------|
| **parent-branch** | PR base for the whole session; confirmed with the user before any branching |
| **issue queue** | Ordered list of issues the user gives in this conversation |
| **queue index** | Current position in the queue (for reporting, e.g. `2/5`) |
| **feature branch** | Branch scoped to one issue: `<number>-<kebab-slug>` |
| **regression gate** | Required local test/build (and lint if CI runs it) before opening a PR |
| **merge-ready** | PR is green, conflicts resolved, review comments triaged |
| **merge policy** | `auto_with_report`: AFK + merge-ready → merge and report; else stop queue |

## Quick start

User invokes with:

1. **Ordered issue list** (required, one or more): numbers, URLs, or paths, in execution order (e.g. `#12` then `#15` then `#20`).
2. **parent-branch** (optional proposal): default to `git branch --show-current`, but **must be confirmed** in Phase 0a — never branch without user confirmation.

A single issue is a queue of length 1.

## Workflow checklist

```
Session:
- [ ] Phase 0a: Confirm parent-branch(Use select UX to decide the answer) + read full issue queue metadata + confirm ordered queue (no auto-append)

For each issue in queue (queue index i/n):
- [ ] Phase 0: Per-issue preconditions (resolve issue, AFK/HITL, Blocked by, dirty tree warn)
- [ ] Phase 1: Sync parent-branch → create feature branch
- [ ] Phase 2: Implement with /tdd
- [ ] Phase 3: Regression gate (all green)
- [ ] Phase 4: Open PR (base = parent-branch, Closes issue) → babysit to merge-ready
- [ ] Phase 5: If AFK (or HITL approved) + merge-ready → merge + cleanup; else STOP entire queue
- [ ] Phase 6: Checkout parent-branch, pull (before next issue)
```

## Phase 0a — Session setup

Do this **once** before the first feature branch. Do not substitute silent detection for user confirmation.

1. **Propose parent-branch**: Show `git branch --show-current` and a short `git status` summary. Ask the user to confirm that branch or name a different **parent-branch**. Record it for the **entire session**; do not change PR base mid-session unless the user says so.
2. **Read full queue metadata before confirming order**: Resolve every issue in the user’s ordered list before starting implementation. For each item, read title, body, acceptance criteria, **Type** (AFK/HITL), and **Blocked by**. Build a queue summary (`index`, issue ref, title, Type, blockers, acceptance summary) and show it to the user before branching.
3. **Confirm issue queue**: Restate the user’s ordered list with the metadata summary. Execution order is **only** this list — do not reorder from **Blocked by**. If an issue’s **Blocked by** references an open blocker or an issue not yet merged in this session (when order violates dependency), stop and list conflicts; ask the user to fix order or close blockers.
4. **Scope**: Run only issues in the confirmed queue; do not add issues automatically.

Discover regression commands once per session via [regression.md](regression.md); reuse the same gate for every issue in the queue.

## Phase 0 — Per-issue preconditions

For the current queue item:

- Resolve the **issue** (title, body, acceptance criteria).
- Read `/to-issues` metadata in the issue body:
  - **Type**: **HITL** → stop the **entire queue** and ask the user before implementing this issue. Continue only for **AFK** or when the user explicitly approves this HITL issue in the session.
  - **Blocked by**: Verify blockers are closed. If incomplete or unclear, **stop the entire queue** and report the blocking issue.
- Warn on a dirty working tree; do not stash unless the user asks.
- One issue per feature branch — never batch multiple issues on one branch.

## Phase 1 — Branch from issue

1. Derive the branch name: `<issue-number>-<kebab-slug-from-issue-title>`  
   Example: `#42` "Add checkout validation" → `42-add-checkout-validation`  
   Slug: lowercase, hyphens, drop punctuation; truncate if the host limits length.
2. Check out **parent-branch** if not already on it.
3. **Always** sync **parent-branch** before each new feature branch: `git fetch`, then `git pull` (or `git pull --ff-only` if the repo requires it). Resolve pull failures with the user; never branch from a stale **parent-branch**.
4. From updated **parent-branch**, run `git checkout -b <feature-branch>`.

## Phase 2 — Implement with TDD

- Read and follow [../tdd/SKILL.md](../tdd/SKILL.md) (vertical RED→GREEN slices; never horizontal “all tests then all code”).
- After the TDD slice is green, use [../tdd-test-supplement/SKILL.md](../tdd-test-supplement/SKILL.md) to add or recommend only focused Vitest unit/component tests and small contract tests. Do not add a new confirmation point for AFK issues.
- Scope to issue acceptance criteria; use `CONTEXT.md` domain language when present.
- Commits and pushes only when user rules allow (default: user must ask to commit).

## Phase 3 — Regression gate

Do **not** open a PR until the regression gate passes on the **feature branch**.

Use [regression.md](regression.md). Run the minimal set that matches CI. If ambiguous, ask the user **once per session**, then reuse for all issues.

On failure: fix in Phase 2, re-run until green.

## Phase 4 — Pull request and babysit

Follow the user’s **creating-pull-requests** rule: parallel `git status` / `git diff` / log vs base, push with `-u`, `gh pr create` with a HEREDOC body.

| Field | Rule |
|-------|------|
| Base | **parent-branch** (not assumed `main`) |
| Title | Concise; tied to the issue |
| Body | Summary, test plan checklist, **`Closes #<number>`** (or tracker equivalent) |

Then follow the **babysit** skill until **merge-ready**:

- CI is green.
- No merge conflicts.
- No unresolved review comments.
- PR body has the correct closing keyword.
- Branch is up to date when the repo requires it.

Babysit ends at merge-ready; Phase 5 decides merge using **merge policy**.

## Phase 5 — Merge and cleanup (policy-driven)

**Auto-merge** when **all** are true:

- Issue is **AFK**, or **HITL** with explicit user approval for this issue in the session.
- PR is **merge-ready** after babysit.
- `gh pr merge` succeeds (prefer the repo’s usual method — inspect recent merged PRs or ask once per repo).

**After auto-merge** (report each step to the user):

1. Checkout **parent-branch**, `git pull`.
2. Delete **feature branch**: local `git branch -d <feature-branch>`; remote `git push origin --delete <feature-branch>` when it exists on remote.
3. Confirm the issue closed (e.g. GitHub auto-close from `Closes #N`).

**Stop the entire queue** (do not start the next issue) when:

- **HITL** and the user has not approved this issue.
- PR is not merge-ready after babysit (including two babysit rounds still not green).
- Merge fails or requires manual intervention you cannot complete safely.
- CI failure appears unrelated to the issue or outside scope.
- Implementation would require expanding beyond the current issue.

Leave an open PR on the feature branch and report PR URL, queue progress, and what blocked continuation.

Git safety: no force-push to **parent-branch**; no `git branch -D` unless merge failed and the user insists.

## Phase 6 — Prepare next issue

Continue to the next queue item **only after Phase 5 merge succeeds** and **parent-branch** has been updated with that merge. Ensure you are on **parent-branch** with `git pull` before Phase 1 for the next issue.

If the user asks to continue without merging the previous issue, treat it as leaving the standard serial workflow: stop, explain that the queue would no longer be `parent-branch → issue branch → PR → merge → updated parent → next issue`, and ask for an explicit new plan before branching.

## Blockers

Stop and ask the user (or stop the queue) when:

- Issue requirements conflict or acceptance criteria are unclear.
- **parent-branch** cannot be pulled cleanly.
- Tests need missing secrets, services, or unavailable infrastructure.
- User queue order conflicts with **Blocked by** and the user has not resolved it.

## Output expectations

At each phase transition, briefly report:

- Queue progress (`i/n`).
- Current branch and **parent-branch**.
- Current issue title.
- Next action.
- Any user decision needed.

**After each issue:**

- PR URL.
- Whether the PR was merged.
- Whether **parent-branch** was updated.
- Regression commands run (or “same as session gate”).

**End of session:**

- Table: completed / blocked / not started issues.
- Regression gate commands for the session.
- Residual risks.

## Examples

**Multi-issue session**

User: parent `develop`, queue `#10` → `#11` → `#12`.

1. Confirm `develop` and queue.
2. `#10`: `10-…` branch → TDD → gate → PR → merge (AFK) → `develop` pull.
3. `#11`: repeat from synced `develop`.
4. If `#12` is HITL and unapproved, stop queue after `#11`; report open PRs if any.

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

- Branching before **parent-branch** is confirmed in Phase 0a
- Using `git branch --show-current` as confirmation without user approval
- Reordering the queue from **Blocked by** instead of the user’s list
- Adding issues not in the confirmed queue
- Implementing on **parent-branch** instead of a feature branch
- Opening the next feature branch without syncing **parent-branch** (`fetch` + `pull`)
- Opening the next feature branch before the previous issue PR has merged into **parent-branch**
- Skipping the regression gate before a PR
- Horizontal TDD (all tests upfront, then all code)
- Retargeting PR base away from **parent-branch** without user approval
- Merging when the issue is **HITL** without approval, or when the PR is not **merge-ready**
- Continuing the queue after a failed merge, unmerged PR, or blocked HITL without a new explicit plan

## Related skills

- [../tdd/SKILL.md](../tdd/SKILL.md) — implementation loop per feature branch
- [../tdd-test-supplement/SKILL.md](../tdd-test-supplement/SKILL.md) — post-TDD Vitest unit/component and small contract supplement pass
- [../e2e-test-issue/SKILL.md](../e2e-test-issue/SKILL.md) — create separate Playwright E2E testing issues from small stable cases into journeys
- [regression.md](regression.md) — discover test/build commands (once per session)
- [../to-issues/SKILL.md](../to-issues/SKILL.md) — **Blocked by** for dependency checks; **order** comes from the user list
- **babysit** — merge-ready loop; Phase 5 applies **merge policy**
