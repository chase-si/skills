---
name: grill-with-docs
description: Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates documentation (CONTEXT.md, ADRs) inline as decisions crystallise. Use when user wants to stress-test a plan against their project's language and documented decisions.
---

<what-to-do>

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the decision tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask exactly one question at a time. Number questions consecutively for the
entire interview, starting with `1.` and continuing with `2.`, `3.`, and so on.
Do not reset the numbering when changing topics or decision-tree branches.

Label answer options with sequential lowercase letters: `a.`, `b.`, `c.`,
`d.`, and so on. Put the recommendation marker immediately after the
recommended answer text, with no intervening space, using this exact format:
`a. xxxxxx(推荐)`.

```text
1. Which approach should we take?

a. First approach(推荐)
b. Second approach
c. Third approach
```

Use an available mouse-selectable single-choice UI for the options whenever
the current environment supports it. Preserve the numbered question and
lettered option labels in that UI. If no selectable UI is available, present
the options as plain text in the same format and ask the user to reply with the
option letter or their own answer. Wait for the answer before continuing.

If a fact can be found by exploring the environment — the codebase, filesystem,
or available tools — look it up instead of asking me. Decisions are mine: put
each decision to me and wait for my answer.

During the interview, write only confirmed domain knowledge to the applicable
`CONTEXT.md` and confirmed architectural decisions to ADRs. Do not modify
product code, configuration, plans, issues, or other implementation artifacts.
Do not implement the plan until I confirm that we have reached a shared
understanding.

</what-to-do>

<supporting-info>

## Domain awareness

During codebase exploration, also look for existing documentation:

### File structure

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has multiple contexts. The map points to where each one lives:

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

Create files lazily — only when you have something to write. If no `CONTEXT.md` exists, create one when the first term is resolved. If no `docs/adr/` exists, create it when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update CONTEXT.md inline

When the user explicitly confirms a term or relationship, update `CONTEXT.md`
right there. Don't batch these up — capture confirmed knowledge as it
crystallises. Never record a recommendation, inference, or unanswered option as
settled domain knowledge. Use the format in
[CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

Don't couple `CONTEXT.md` to implementation details. Only include terms that are meaningful to domain experts.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true, and write it only after
the user confirms the decision:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).

</supporting-info>
