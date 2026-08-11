---
name: issue-driven-dev
description: >-
  Runs an ordered issue queue serially on one feature branch per issue: confirm
  parent branch, validate AFK/HITL and blockers, implement with TDD, pass the
  regression gate, commit a review candidate, pass two-axis code review, open
  PRs, babysit, and auto-merge only approved merge-ready work. Use for issue-driven development, implementing issue #N then #M,
  按顺序实现多个 issue, or branch → TDD → PR → merge flow.
---

# Issue-Driven Development

Run a user-provided **issue queue** in order:

`confirm parent-branch + queue → issue branch → TDD → regression gate → commit → code review → PR → babysit → merge if allowed → refresh parent-branch → next issue`

This is an orchestrator skill. Keep implementation details in the referenced
skills and files:

- [../tdd/SKILL.md](../tdd/SKILL.md) for RED→GREEN→REFACTOR implementation.
- [../tdd-test-supplement/SKILL.md](../tdd-test-supplement/SKILL.md) after the TDD slice is green.
- [../code-review/SKILL.md](../code-review/SKILL.md) for separate Standards and Spec review before opening a PR.
- [regression.md](regression.md) for discovering the repo's PR-before-merge gate.
- [../to-issues/SKILL.md](../to-issues/SKILL.md) for issue metadata conventions: **Type** and **Blocked by**.
- [../e2e-test-issue/SKILL.md](../e2e-test-issue/SKILL.md) for separate Playwright coverage issues; if E2E work is already in the queue, treat it as a normal issue.

## Core Rules

- Confirm the **parent-branch** with the user before creating any branch.
- Resolve every queue item before starting, then confirm the exact ordered queue.
- Never reorder from **Blocked by**; stop if the user-provided order violates open dependencies.
- Run one issue per feature branch. Do not batch issues.
- Keep the queue serial even when multiple issues have no blockers. Parallel
  frontier scheduling requires a separate explicit plan and is outside this
  workflow.
- Sync **parent-branch** before each feature branch and after each merge.
- Do not review an uncommitted candidate: the two-axis review compares
  **parent-branch** to `HEAD`, so the complete candidate must be committed first.
- Do not open a PR until the committed candidate passes the regression gate and
  two-axis review on the feature branch.
- Do not start the next issue until the current issue PR has merged into **parent-branch**.
- Stop the queue on unresolved HITL, failed merge, stale/conflicting parent branch, unclear requirements, or unrelated CI failure.
- If the user asks to continue before the current PR merges, stop and ask for a new explicit plan; that leaves the serial workflow.

## Terms

| Term | Meaning |
| --- | --- |
| **parent-branch** | The PR base for the whole session; user-confirmed once before branching |
| **issue queue** | Ordered issues, URLs, or local issue files supplied by the user |
| **Type** | `/to-issues` metadata: **AFK** can proceed; **HITL** requires approval |
| **Blocked by** | `/to-issues` dependency list; blockers must already be closed or merged earlier in this queue |
| **regression gate** | Local commands matching CI enough to open a PR |
| **merge-ready** | CI green, no conflicts, review comments addressed, closing keyword present |

## Session Setup

Do this once before the first feature branch.

1. Inspect current branch and working tree.
2. Propose the current branch as **parent-branch** unless the user provided one.
3. Ask the user to confirm **parent-branch** or name a different one.
4. Resolve every queue item: title, body, acceptance criteria, **Type**, **Blocked by**.
5. Discover the regression gate once via [regression.md](regression.md).
6. Show a queue summary: index, issue ref, title, Type, blockers, acceptance summary.
7. Ask the user to confirm the queue order before branching.

Do not silently treat branch detection or dependency analysis as user approval.
Run only the confirmed queue; do not append related issues automatically.

## Per-Issue Flow

For each issue `i/n`:

1. **Preconditions**
   - Re-read the issue if it may have changed.
   - Stop on open or unresolved **Blocked by** entries.
   - Once all blockers are resolved, synchronize readiness before starting:
     apply `ready-for-agent` to AFK or `ready-for-human` to HITL, and remove the
     configured blocked label when the tracker supports these labels.
   - Stop on unapproved **HITL**.
   - Warn on dirty working tree; do not stash without user request.

2. **Branch**
   - Check out **parent-branch**.
   - `git fetch`, then `git pull` or the repo's required fast-forward equivalent.
   - Derive the branch name as `<issue-number>-<kebab-slug-from-issue-title>`.
   - Example: `#42` "Add checkout validation" -> `42-add-checkout-validation`.
   - Slug rules: lowercase, hyphen-separated, drop punctuation, truncate if the host limits length.
   - Create the derived branch from the synced **parent-branch**.

3. **Implement**
   - Follow [../tdd/SKILL.md](../tdd/SKILL.md) in vertical RED→GREEN slices.
   - Scope strictly to the issue acceptance criteria.
   - Run the closest relevant single test file during each slice so failures
     stay local and diagnostic.
   - When the repo exposes a typecheck command, run it regularly, especially
     after changing public interfaces, schemas, generated types, or adapters.
   - After green/refactor, follow [../tdd-test-supplement/SKILL.md](../tdd-test-supplement/SKILL.md).
   - Do not add Playwright here unless the current issue is explicitly an E2E issue.

4. **Gate**
   - Run the session regression gate on the feature branch.
   - Fix failures in the implementation phase and re-run until green.

5. **Commit Review Candidate**
   - Confirm the working tree contains only changes owned by the current issue.
     Stop rather than committing unrelated user changes.
   - Commit the complete green candidate using the repository's commit
     conventions and include the issue reference when the tracker supports it.
   - Confirm `git diff <parent-branch>...HEAD` is non-empty and represents the
     intended issue scope.

6. **Review**
   - Run [../code-review/SKILL.md](../code-review/SKILL.md) against
     **parent-branch** as the fixed point and the current issue as the spec.
   - Address actionable Standards and Spec findings without expanding beyond
     the issue acceptance criteria.
   - If review changes code, run the relevant single test files and typecheck
     while fixing, re-run the full regression gate, commit the review fixes,
     and repeat both review axes against **parent-branch** until clear.

7. **PR**
   - Push the feature branch when ready to open the PR.
   - Create the PR with **parent-branch** as base.
   - Include summary, test plan, and `Closes #<issue-number>` or tracker equivalent.
   - Babysit until **merge-ready**.

8. **Merge Decision**
   - Auto-merge only when the issue is **AFK** and the PR is **merge-ready**.
   - For **HITL**, merge only after explicit user approval in this session.
   - Use the repo's normal merge method; inspect recent merged PRs or ask once if unclear.

9. **Cleanup**
   - After merge succeeds, check out **parent-branch** and pull.
   - Delete the local feature branch with `git branch -d`.
   - Delete the remote feature branch when it exists.
   - Confirm the issue closed or report if it did not.

If any step cannot be completed safely, leave the PR/branch as-is, stop the queue,
and report the blocker and remaining issues.

## Merge Policy

Default policy: `auto_with_report`.

Auto-merge is allowed only when all are true:

- Issue Type is **AFK**, or **HITL** has explicit user approval.
- PR is **merge-ready** after babysitting.
- The merge command succeeds without manual intervention.

Never force-push to **parent-branch**. Never delete an unmerged branch with
`git branch -D` unless the user explicitly asks.

## Output Contract

At each phase transition, report briefly:

- Queue progress (`i/n`)
- Current issue title
- Current branch and **parent-branch**
- Next action
- Any user decision needed

After each issue, report:

- PR URL
- Whether it merged
- Whether **parent-branch** was refreshed
- Candidate and review-fix commits created
- Regression commands run

At the end of the queue, report:

- Completed issues
- Blocked issue, if any, with reason
- Not-started issues
- Session regression gate
- Residual risks

## Stop Conditions

Stop the entire queue when:

- The user has not confirmed **parent-branch** or queue order.
- A **HITL** issue is next and lacks explicit approval.
- A blocker is open, missing, ambiguous, or ordered after the blocked issue.
- **parent-branch** cannot be pulled cleanly.
- Requirements conflict or acceptance criteria are unclear.
- Tests need unavailable secrets, services, or infrastructure.
- Regression or CI fails for an unrelated reason.
- Code review finds a requirements conflict or a material issue that cannot be
  resolved within the current issue.
- PR remains not merge-ready after reasonable babysitting.
- Merge fails or needs manual intervention.
- Continuing would require expanding beyond the current issue.
- The user asks to start the next issue before the current PR is merged, without a new explicit plan.

## Anti-Patterns

- Branching before **parent-branch** and queue order are confirmed.
- Implementing directly on **parent-branch**.
- Reordering the queue from dependency metadata.
- Adding issues that were not confirmed in the queue.
- Opening a PR before the regression gate is green.
- Running the two-axis review before the complete candidate is committed.
- Committing unrelated working-tree changes with the current issue.
- Starting the next issue before the previous PR is merged into **parent-branch**.
- Treating TDD as "write all tests, then write all code."
- Mixing broad E2E coverage into ordinary feature issues.
- Merging HITL work without explicit approval.
- Continuing after a failed merge, unmerged PR, or blocked issue without a new plan.
