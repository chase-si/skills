---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Load `tdd` with the environment's skill-loading tool, or read
[its instructions](../tdd/SKILL.md) when no such tool is available. Use it where
possible at pre-agreed seams, preserving its AFK/HITL and acceptance-evidence rules.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Commit the complete green candidate to the current branch before review, so
the `HEAD` diff includes the implementation. Then load `code-review` or read
[its instructions](../code-review/SKILL.md) to review the work against the fixed
point and originating issue or PRD.

Address actionable findings, rerun affected checks, and commit review fixes to
the same branch. Repeat the review when those fixes warrant it.

If the requested task includes a PR, load `pr` or read
[its instructions](../pr/SKILL.md) when writing the body. This entrypoint does
not add the serial queue or merge policy provided by `/issue-driven-dev`.
