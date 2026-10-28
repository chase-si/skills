---
name: pr
description: "Write pull request descriptions with a clear summary, before/after evidence, and concrete merge impact. Use when drafting or updating a PR body."
---

# Pull Request Description

Shape the PR body around what changed, why it works, and what merging affects.
Follow the repository's PR template when one exists, placing this information
in its corresponding sections. This skill formats the body; permission to
publish, push, merge, or change access comes from the surrounding task.

## Summary

Lead with the concrete problem and resulting behavior. Use a short paragraph
for a small change. When a relationship is hard to explain in prose, use the
smallest useful visual: a diff, pseudocode, a call tree, a file tree, or Mermaid.
Name the affected behavior and scope; omit abandoned approaches and chat history.

## Evidence

Show the evidence actually obtained. For a bug, pair the failing symptom or
test before the fix with the passing result after it. For a feature, cite the
relevant test result, output, or screenshot. Redact credentials and private data.
State what has not been verified; never invent a before run or imply a human
check passed because an automated test did.

When the source uses stable acceptance IDs, preserve the `AC-N` evidence map:

| Acceptance | Public seam | Evidence | Result / pending human check |
| --- | --- | --- | --- |
| Source `AC-N` | Agreed interface | Test case, file, output, or screenshot | Observed result or required approval |

Keep the repository's test plan and closing issue reference, including
`Closes #<issue-number>` or its tracker equivalent.

## Merge Impact

State the concrete blast radius and reversibility. Is this a two-way door
(straightforward rollback) or a one-way door (data loss, irreversible migration,
external side effect)? Explain the affected callers, data, or users and any
necessary rollout or recovery action. For a small reversible change, one
sentence is enough. Flag uncertainty when the impact cannot be established.
