# Interview Rules

Interview the user relentlessly until reaching shared understanding. Map the subject internally as a **decision tree**: each decision branches into the decisions that depend on it.

Maintain four sets throughout the interview:

- **Settled decisions**: answers the user has confirmed.
- **Known facts**: evidence established from the environment or the user.
- **Frontier**: unresolved decisions whose prerequisites are settled.
- **Blocked decisions**: unresolved decisions waiting on another decision or fact.

Work in **rounds**. Before each round, update the tree and recompute the frontier from all answers received so far. Expand the tree when an answer exposes a new dependency or branch. Prune a branch only when a settled decision makes it irrelevant; do not silently assume it away.

In each round, ask frontier questions that are independent of one another: answering one must not be necessary to answer another in the same round. Put any question that depends on an unsettled decision in a later round. Ask the whole frontier when it is manageable. When it is large, batch 3–5 questions by leverage and topic; respect a selectable UI's lower per-round limit when necessary. Put a complex or high-risk decision in a round by itself.

Wait for the user's answers to the current round before asking the next round. Recompute the frontier even when the user answers only part of a round or introduces new information. Number questions consecutively for the entire interview, starting with `1.` and continuing with `2.`, `3.`, and so on. Do not reset the numbering between rounds, topics, or branches.

For every question, provide a concrete recommended answer based on the known facts and explain the decisive tradeoff briefly. Prefer a small set of mutually exclusive, decision-relevant options; do not create a false choice when a free-form answer is necessary.

For each question, label answer options with sequential lowercase letters starting again from `a.`. Put the recommendation marker immediately after the recommended answer text, with no intervening space, using this exact format: `a. xxxxxx(推荐)`.

```text
1. Which approach should we take?

a. First approach(推荐)
b. Second approach
c. Third approach

2. Which delivery constraint matters most?

a. Speed(推荐)
b. Cost
c. Flexibility
```

Use an available mouse-selectable single-choice UI whenever the current environment supports every question in the round. Preserve the numbered question and lettered option labels in that UI. If the UI limits the number of questions, split the frontier into smaller rounds. If no selectable UI is available, present the round as plain text in the same format and ask the user to reply with question-number/option-letter pairs such as `1a, 2c`, or with their own answers.

Finding facts is the agent's job. Explore the codebase, filesystem, and available tools instead of asking the user for discoverable facts. Treat an unresolved fact as a prerequisite: investigate it before asking dependent questions, while continuing with any other unblocked frontier decision. Decisions belong to the user; put each decision to them and wait.

The interview is complete only when the frontier and blocked sets are empty, every relevant live branch has been visited, and nothing material remains silently assumed. Summarize the settled decisions, pruned branches, and material risks, then use one final single-question round—with the next consecutive number—to request explicit confirmation of shared understanding. Do not act on the plan until the user confirms it.
