---
name: retro
description: "Review a coding session and suggest improvements to the agent's environment, checks, navigation, and instructions."
disable-model-invocation: true
---

# Retrospective

Review the session the user names, defaulting to the current one. Suggest
environment changes that would make the next run more reliable. This is a
recommendation workflow; implement selected changes only when the user asks.

## Process

1. Load `writing-great-skills` with the environment's skill-loading tool, or
   read [its instructions](../../public/writing-great-skills/SKILL.md) when no
   such tool is available.
2. Read the session's primary evidence: the conversation, tool output, diffs,
   or the requested local session log. After context has been cleared, use
   the original log rather than reconstructing events from memory. If it is
   unavailable, state the evidence gap and limit conclusions accordingly.
3. Inspect the repo's existing instructions, check commands, hooks, and CI
   before recommending new ones. Distinguish a missing guardrail from an
   existing check that was not run or is broken.
4. Present actionable candidates in severity order. Tie each one to an observed
   event, the proposed change and location, its benefit, and any cost. If no
   supported candidate exists, say so rather than inventing work.

## What to look for

- **Navigation:** repeated searching or hidden dependencies that a precise
  pointer would have resolved.
- **Automated checks:** mechanical mistakes a linter, typecheck, test, hook, or
  CI job could catch. Prefer wiring or fixing an existing check. A repo with no
  guardrail is a candidate; assess its value against this repo's actual work.
- **Coding standards:** genuine judgement calls missed during review. Put
  mechanical rules in deterministic checks instead of adding prose reminders.
- **Instructions:** duplicated, stale, contradictory, or ineffective guidance.
  Keep always-loaded `AGENTS.md` / `CLAUDE.md` concise and point to maintained
  references for conditional detail.
- **Tool economy and information:** costly repeated calls, missing logs, or
  inaccessible evidence. Recommend the smallest useful access or tool change.

Keep a missing test seam as a concrete finding. Recommend
`/improve-codebase-architecture` when it warrants design work; do not launch
that user-only workflow or fold a refactor into the completed fix.
