---
name: to-issues
description: Break a plan, spec, or PRD into independently-grabbable issues with portable capability/reasoning requirements and model recommendations, using vertical slices or expand-contract sequencing. Use when the user wants to create implementation tickets, model blockers, or break work into issues.
---

# To Issues

Break a plan into independently-grabbable issues using vertical slices (tracer bullets).

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-matt-pocock-skills` if not.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference (issue number, URL, or path) as an argument, fetch it from the issue tracker and read its full body and comments.

Identify the authoritative source for the breakdown. When it is a PRD, retain
its tracker reference or path and its current revision where the tracker exposes
one. Read its user stories, stable acceptance IDs (`AC-N`), test seams, and
verification decisions; do not regenerate or renumber those identifiers.

Collect the user's available models and native reasoning settings from the
conversation or project configuration, including any existing routing table.
Use the latest explicit preferences over project defaults. Missing model
configuration should not block decomposition; mark recommendations as unmapped
where necessary instead of inventing model capabilities or settings.

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

For a PRD-derived breakdown, create a coverage map before publishing:

- Every issue names the `US-N` and `AC-N` identifiers it covers.
- Every source acceptance criterion is owned by at least one issue.
- Every covered acceptance criterion carries forward its agreed test seam and
  verification method.
- If decomposition reveals a missing or contradictory acceptance criterion,
  return to the PRD instead of inventing an untraceable issue-only requirement.
- If more than one issue intentionally shares an acceptance criterion, explain
  which part each issue proves and where final verification occurs.

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

### 4. Recommend an execution profile

Give every issue two independent, provider-neutral requirements, then map them
to a concrete model and its native setting. The profile describes the task;
the model recommendation is replaceable. Do not put model names in issue titles.

| Axis | Value | Use when |
| --- | --- | --- |
| Capability | `light` | The approach is established, changes are bounded or mechanical, and failures are easy to detect. |
| Capability | `standard` | Ordinary feature work needs competent coding and integration across known interfaces. |
| Capability | `strong` | Novel design, subtle debugging, concurrency, security boundaries, or difficult compatibility constraints require stronger judgment. |
| Reasoning | `low` | A direct, well-specified path needs few dependent decisions. |
| Reasoning | `medium` | Several interacting decisions or edge cases need deliberate checking. |
| Reasoning | `high` | Long dependency chains, competing hypotheses, or hard-to-observe failure modes need sustained reasoning. |

Start ordinary feature slices at `standard / medium` and adjust from evidence.
Assess uncertainty, coupling, and the consequences of a mistake, not just line
count or issue length. The axes are independent: `standard / high` and
`strong / medium` are both valid. More reasoning does not automatically make a
weaker model satisfy a stronger capability requirement. A stronger model also
does not remove the need for a HITL decision or the agreed verification.

Use a reusable routing table, supplied by the user or proposed in the breakdown
overview, with these columns:

| Capability | Reasoning demand | Preferred model / native setting | Alternatives | Fit evidence |
| --- | --- | --- | --- | --- |
| Profile tier | Profile demand | Exact configured name or user alias / supported setting | Other suitable model / setting pairs, or none confirmed | User-supplied mapping, observed project results, tentative assessment, or unmapped |

One model can appear in several rows with different native settings. Treat
user-provided names such as `grok4.7`, `gpt 6.1 sol`, and `gemini flash3.8` as
aliases until their exact identities and supported controls are known. Do not
infer a capability ranking from a brand, version number, or words like "Flash".
Likewise, different providers' `High` settings are not an equivalence scale.
The profile's `low / medium / high` describes reasoning demand; it is not an
API parameter to copy blindly. If a model has no configurable reasoning control,
record `default (not configurable)`; leave its fit uncalibrated unless evidence
supports it. If the supported controls are unknown, record `setting unconfirmed`
instead of assuming a default or a reasoning knob. Verify current model details
from authoritative documentation when needed to make a concrete recommendation.

Prefer the least costly suitable configured pair when cost is known; otherwise
follow the user's preference without claiming a price ranking. Label an
unvalidated assignment `tentative`; if no defensible mapping exists, write
`unmapped` and list the available candidate aliases once in the overview.
Never silently substitute an unavailable model or promise equivalent results.

For each issue, record:

- **Execution profile:** `capability: light|standard|strong; reasoning: low|medium|high`.
- **Recommended model:** model alias / native setting, with fit evidence or `tentative`; otherwise `unmapped`.
- **Alternatives:** suitable configured pairs, or `none confirmed`.
- **Rationale / escalation:** one sentence tying the profile to this issue's uncertainty or risk, plus a concrete trigger to reconsider the profile, such as discovering a concurrency invariant or a failure the agreed tests cannot expose.

Show the shared routing table once, not in every issue. Switching models means
selecting another suitable pair for the same profile; update the recommendation
without changing acceptance criteria or readiness. Revise the profile only when
new task evidence warrants it. Recommendations are advisory metadata, not
authorization to change runtime settings, and do not create a blocker or HITL
requirement by themselves. Do not create new tracker labels for these fields
unless the project's label vocabulary already defines them.

### 5. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Execution profile and model**: capability and reasoning requirements, recommended model / native setting, fit evidence, alternatives, and a brief rationale / escalation trigger
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)
- **Acceptance criteria covered**: which stable `AC-N` identifiers this slice owns
- **Test seams and verification**: how the covered criteria will be proved

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?
- Do the execution profiles and model mappings fit the available models and your cost/quality preferences?

Iterate until the user approves the breakdown.

Before publishing, show the aggregate `US-N`/`AC-N` coverage map and resolve
every uncovered acceptance criterion, unexplained overlap, or verification gap.

### 6. Publish the issues to the configured tracker

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

**Execution profile:** `capability: light|standard|strong; reasoning: low|medium|high`

**Recommended model:** model alias / native setting; fit evidence or `tentative`, or `unmapped`

**Alternatives:** suitable model / native setting pairs, or `none confirmed`

**Rationale / escalation:** why this profile fits; concrete trigger to reassess

**Derived from:** Authoritative source reference or path; for a PRD, include its revision when available

**Covers:** `US-N`; `AC-N`

**What to build:** the end-to-end behavior this issue makes work, from the
user's perspective.

**Blocked by:** issue numbers and titles, or "None — can start immediately".

**Status:** `ready-for-agent`, `ready-for-human`, or the configured blocked
status, according to Type and currently unresolved blockers

## Acceptance criteria

- [ ] `AC-N` — Full observable criterion carried forward from the PRD

## Test seams

- `AC-N` — Highest practical public interface used to prove the criterion

## Verification

- `AC-N` — Automated or human, with the reason carried forward from the PRD

</local-issue-template>

<issue-template>
## Parent

A reference to the parent issue on the issue tracker (if the source was an existing issue, otherwise omit this section).

## Derived from

The authoritative source reference or path. When the source is a PRD, include
the revision used for this breakdown when available.

## Type

AFK or HITL.

## Execution recommendation

- **Profile:** `capability: light|standard|strong; reasoning: low|medium|high`
- **Recommended model:** model alias / native setting; fit evidence or `tentative`, or `unmapped`
- **Alternatives:** suitable model / native setting pairs, or `none confirmed`
- **Rationale / escalation:** why this profile fits; concrete trigger to reassess

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it here and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Covers

- **User stories:** `US-N`
- **Acceptance criteria:** `AC-N`

## Acceptance criteria

- [ ] `AC-N` — Full observable criterion carried forward from the PRD

## Test seams

- `AC-N` — Highest practical public interface through which the criterion is verified

## Verification

- `AC-N` — Automated or human, with the reason for that choice

## Blocked by

- References to every blocking issue

Or "None — can start immediately" if there are no blockers.

</issue-template>
