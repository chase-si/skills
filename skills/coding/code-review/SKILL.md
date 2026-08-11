---
name: code-review
description: Review the changes since a fixed point (commit, branch, tag, or merge-base) along two axes — Standards (does the code follow this repo's documented coding standards?) and Spec (does the code match what the originating issue/spec asked for?). Runs both reviews in parallel sub-agents and reports them side by side. Use when the user wants to review a branch, a PR, work-in-progress changes, or asks to "review since X".
---

Two-axis review of the diff between `HEAD` and a fixed point the user supplies:

- **Standards** — does the code conform to this repo's documented coding standards?
- **Spec** — does the code faithfully implement the originating issue / prd / spec?

Both axes run as **parallel sub-agents** so they don't pollute each other's context, then this skill aggregates their findings.

The issue tracker should have been provided to you — run `/setup-matt-pocock-skills` if `docs/agents/issue-tracker.md` is missing.

## Process

### 1. Pin the fixed point

Whatever the user said is the fixed point — a commit SHA, branch name, tag, `main`, `HEAD~5`, etc. If they didn't specify one, ask for it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. A bad ref or empty diff should fail here — not inside two parallel sub-agents.

### 2. Identify the spec source

Build the originating specification chain in this order:

1. **Current issue** — find issue references in commit messages (`#123`,
   `Closes #45`, GitLab `!67`, etc.) and fetch the full issue via the workflow in
   `docs/agents/issue-tracker.md`. The issue defines the current slice's scope,
   acceptance criteria, test seams, and verification decisions.
2. **Parent PRD** — follow the issue's `Derived from` or `Parent` reference and
   fetch the full PRD. The PRD defines the user intent, stable `US-N`/`AC-N`
   mapping, cross-cutting constraints, and out-of-scope boundaries. Record the
   referenced revision when one is available; if it no longer matches the
   current PRD revision, report source drift before judging implementation.
3. **Architectural decisions** — read ADRs relevant to the changed area. They
   constrain the implementation but do not expand the issue's product scope.
4. **Explicit source** — include a path the user passed, or a matching spec
   under `docs/`, `specs/`, or `.scratch/`, when the issue chain is absent or the
   user names it as an additional source.

Treat the current issue as the unit of delivery: do not report unrelated parts
of the parent PRD as missing from this issue. Use the parent PRD to verify that
the issue's `US-N`/`AC-N` claims are accurate and that the implementation does
not violate feature-wide constraints. If the issue, PRD, and ADRs contradict
one another, report the conflict rather than silently choosing a source.

If no current issue or other spec can be found, ask the user where the spec is.
If they say there isn't one, the **Spec** sub-agent will skip and report
"no spec available".

### 3. Identify the standards sources

Anything in the repo that documents how code should be written, such as `CODING_STANDARDS.md` or `CONTRIBUTING.md`.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below — a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation — and, like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name** — a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code** — the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy** — a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps** — the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession** — a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches** — the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery** — one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change** — one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality** — abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains** — long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man** — a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest** — a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

### 4. Spawn both sub-agents in parallel

**Standards sub-agent prompt** — include:

- The full diff command and commit list.
- The list of standards-source files you found in step 3, **plus the smell baseline from step 3** pasted in full — the sub-agent has no other access to it.
- The brief: "Report — per file/hunk where relevant — (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls — documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent prompt** — include:

- The diff command and commit list.
- The current issue, parent PRD, relevant ADRs, and any explicit spec source,
  clearly labelled. Include their paths or fetched contents and the issue's
  claimed `US-N`/`AC-N` coverage, test seams, and verification decisions.
- The precedence rule: review the current issue as the delivery scope; use the
  parent PRD for traceability and feature-wide constraints; use ADRs as
  architectural constraints. Report contradictions instead of resolving them.
- The brief: "Report: (a) issue acceptance criteria that are missing, partial,
  or not proved at the specified seam; (b) incorrect `US-N`/`AC-N` traceability
  to the parent PRD; (c) behaviour not requested by the current issue or allowed
  by the PRD (scope creep); (d) requirements that look implemented but whose
  behaviour appears wrong; (e) deviations from the agreed verification method;
  and (f) conflicts with relevant ADRs. Do not expect this issue to implement
  unrelated parts of the parent PRD. Quote the source and stable identifier for
  each finding. Under 400 words."

If the spec is missing, skip the Spec sub-agent and note this in the final report.

### 5. Aggregate

Present the two reports under `## Standards` and `## Spec` headings, verbatim or lightly cleaned. Do **not** merge or rerank findings — the two axes are deliberately separate (see _Why two axes_).

End with a one-line summary: total findings per axis, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes — that's the reranking the separation exists to prevent.

## Why two axes

A change can pass one axis and fail the other:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.
