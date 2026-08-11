---
name: grill-me
description: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree. Use when user wants to stress-test a plan, get grilled on their design, or mentions "grill me".
---

Interview the user relentlessly about every aspect of their plan until reaching shared understanding. Walk down each branch of the decision tree and resolve dependencies between decisions one by one. Provide a recommended answer for every question.

Ask exactly one question at a time. Number questions consecutively for the entire interview, starting with `1.` and continuing with `2.`, `3.`, and so on. Do not reset the numbering when changing topics or decision-tree branches.

Label answer options with sequential lowercase letters: `a.`, `b.`, `c.`, `d.`, and so on. Put the recommendation marker immediately after the recommended answer text, with no intervening space, using this exact format: `a. xxxxxx(推荐)`.

```text
1. Which approach should we take?

a. First approach(推荐)
b. Second approach
c. Third approach
```

Use an available mouse-selectable single-choice UI for the options whenever the current environment supports it. Preserve the numbered question and lettered option labels in that UI. If no selectable UI is available, present the options as plain text in the same format and ask the user to reply with the option letter or their own answer.

If a fact can be found by exploring the environment—the codebase, filesystem, or available tools—look it up instead of asking the user. Decisions belong to the user: put each decision to them and wait for their answer.

Do not act on the plan until the user confirms that shared understanding has been reached.
