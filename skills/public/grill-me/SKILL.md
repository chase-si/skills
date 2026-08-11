---
name: grill-me
description: Interview the user relentlessly about a plan, decision, design, or idea until reaching shared understanding. Use a dependency-aware decision tree, ask one question at a time, and revisit the frontier after every answer. Use when the user wants to stress-test their thinking, be grilled on a plan or design, or uses any "grill me" or similar trigger phrase.
---

Interview the user relentlessly until reaching shared understanding. Map the subject internally as a **decision tree**: each decision branches into the decisions that depend on it.

Maintain four sets throughout the interview:

- **Settled decisions**: answers the user has confirmed.
- **Known facts**: evidence established from the environment or the user.
- **Frontier**: unresolved decisions whose prerequisites are settled.
- **Blocked decisions**: unresolved decisions waiting on another decision or fact.

After every answer, update the tree and recompute the frontier. Expand the tree when an answer exposes a new dependency or branch. Prune a branch only when a settled decision makes it irrelevant; do not silently assume it away.

Ask exactly one frontier question at a time, choosing the highest-leverage decision first. A question that depends on an unsettled decision belongs to a later turn. Wait for the user's answer before asking the next question. Number questions consecutively for the entire interview, starting with `1.` and continuing with `2.`, `3.`, and so on. Do not reset the numbering when changing topics or branches.

For every question, provide a concrete recommended answer based on the known facts and explain the decisive tradeoff briefly. Prefer a small set of mutually exclusive, decision-relevant options; do not create a false choice when a free-form answer is necessary.

Label answer options with sequential lowercase letters: `a.`, `b.`, `c.`, `d.`, and so on. Put the recommendation marker immediately after the recommended answer text, with no intervening space, using this exact format: `a. xxxxxx(推荐)`.

```text
1. Which approach should we take?

a. First approach(推荐)
b. Second approach
c. Third approach
```

Use an available mouse-selectable single-choice UI for the options whenever the current environment supports it. Preserve the numbered question and lettered option labels in that UI. If no selectable UI is available, present the options as plain text in the same format and ask the user to reply with the option letter or their own answer.

Finding facts is the agent's job. Explore the codebase, filesystem, and available tools instead of asking the user for discoverable facts. Treat an unresolved fact as a prerequisite: investigate it before asking dependent questions, while continuing with any other unblocked frontier decision. Decisions belong to the user; put each decision to them and wait.

The interview is complete only when the frontier and blocked sets are empty, every relevant live branch has been visited, and nothing material remains silently assumed. Summarize the settled decisions, pruned branches, and material risks, then ask one final consecutively numbered question requesting explicit confirmation of shared understanding. Do not act on the plan until the user confirms it.
