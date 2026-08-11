---
name: to-issues
description: Break a plan, spec, or PRD into independently-grabbable issues using tracer-bullet vertical slices or expand-contract sequencing for wide refactors. Use when the user wants to create implementation tickets, model blockers, or break work into issues.
---

# To Issues

Break a plan into independently-grabbable issues using vertical slices (tracer bullets).

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-matt-pocock-skills` if not.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference (issue number, URL, or path) as an argument, fetch it from the issue tracker and read its full body and comments.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Issue titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

### 3. Draft vertical slices

Break the plan into **tracer bullet** issues. Each issue is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices may be 'HITL' or 'AFK'. HITL slices require human interaction, such as an architectural decision or a design review. AFK slices can be implemented and merged without human interaction. Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Each slice fits in one fresh agent context window
- Prefer many thin slices over few thick ones
- Put any prefactoring needed to make the change easy before the dependent slices
</vertical-slice-rules>

**Wide refactors are the exception to vertical slicing.** A wide refactor is one
mechanical change whose blast radius crosses much of the codebase, so no narrow
slice can merge green by itself. Sequence it as **expand–contract**:

1. **Expand** — introduce the new form beside the old one without breaking callers.
2. **Migrate** — move callers in batches sized by blast radius, each blocked by
   the expand issue. Keep CI green while both forms coexist.
3. **Contract** — remove the old form after every migration issue is complete.

If migration batches cannot stay green independently, use a shared integration
branch and make them all block a final integrate-and-verify issue. Do not force
a wide mechanical refactor into fake end-to-end slices.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?

Iterate until the user approves the breakdown.

### 5. Publish the issues to the configured tracker

Publish in dependency order, blockers first. Preserve the same issue content
regardless of tracker; only the representation of parent and blocking edges
changes:

- **Local Markdown** — write one file per issue under
  `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01` in
  dependency order. Never combine the issue set into one file.
- **GitHub, GitLab, or another real tracker** — create one issue per slice. Use
  native sub-issues for the parent relationship and native blocking edges where
  the tracker supports them. Keep `## Parent` and `## Blocked by` in the body as
  the fallback when it does not.

Apply the `issue` workflow label to every published issue when the tracker
supports labels. Treat readiness as current state, not eventual intent:

- An AFK issue with no unresolved blockers receives `ready-for-agent`.
- A HITL issue with no unresolved blockers receives `ready-for-human`.
- An issue with any unresolved blocker receives neither ready label. Apply the
  configured blocked label when one exists; otherwise leave it without a ready
  triage label and rely on its explicit blocking edges.

Do not mark a dependent issue ready merely because its blockers were published
earlier; they must actually be closed or otherwise satisfied according to the
tracker. Do not close or modify the parent.

<local-issue-template>

# <NN> — <Issue title>

**Type:** AFK or HITL

**What to build:** the end-to-end behavior this issue makes work, from the
user's perspective.

**Blocked by:** issue numbers and titles, or "None — can start immediately".

**Status:** `ready-for-agent`, `ready-for-human`, or the configured blocked
status, according to Type and currently unresolved blockers

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2

</local-issue-template>

<issue-template>
## Parent

A reference to the parent issue on the issue tracker (if the source was an existing issue, otherwise omit this section).

## Type

AFK or HITL.

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- References to every blocking issue

Or "None — can start immediately" if there are no blockers.

</issue-template>
