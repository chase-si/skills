# Skill mechanics

The skill-specific branch of [`writing-great-skills`](SKILL.md): what changes when the document is a skill (frontmatter, the invocation choice, and router skills). Everything else about writing it is the universal reference in `SKILL.md`.

## Invocation

Two choices, trading the two loads:

- A **model-invoked** skill keeps a `description`, so the agent can fire it autonomously, and other skills can reach it. You can still type its name: model-invocation always _includes_ user reach; a description only ever adds agent discovery, never removes the human's. The description is the skill's top-level context pointer, forced to stay loaded at all times: permanent context load in exchange for discoverability. A model-invoked skill whose content is all reference is also one home for shared reference: another skill can invoke it, so reference needed by several skills lives in one place. Mechanics: omit `disable-model-invocation` and omit Codex's `policy.allow_implicit_invocation` (or set it to `true`) in `agents/openai.yaml`, and write a model-facing description carrying the trigger branches (the pointer-writing rules in `SKILL.md` apply in full).
- A **user-invoked** skill strips the description from the agent's reach: only the human typing its name can invoke it, and no other skill can. Zero context load, but it spends cognitive load: you are the index that must remember it exists. Mechanics: set `disable-model-invocation: true` and Codex's `policy.allow_implicit_invocation: false` in `agents/openai.yaml`; the `description` becomes human-facing: a one-line summary, trigger lists stripped.

Preserve an existing skill's invocation policy unless the user authorizes a
change. For a new skill, keep normal discovery unless the user wants an
explicit-only entrypoint. Shared model-invoked references must remain reachable
by their callers.

Two user-invoked entrypoints can share a plain reference file. Read that file
inside the already-authorized workflow instead of trying to invoke a hidden
entrypoint. A compatibility entrypoint may also explicitly read the primary
entrypoint's instructions as reference; keep one authoritative workflow body.

## Loading dependencies

For a model-invoked dependency, use the environment's skill-loading tool when
available. Otherwise read its linked `SKILL.md` before following it. Naming
`/skill` in prose alone is not a loading instruction. Load multiple required
skills separately. For a missing user-only setup or a new user-only workflow,
recommend the exact command to the user rather than invoking it autonomously.

## Splitting by invocation

The invocation cut of splitting (the sequence cut lives in `SKILL.md`): split off a model-invoked skill when you have a distinct leading word that should trigger it on its own (a trigger word you actually use in your prompts), or another skill must reach it. You pay context load for the new always-loaded description, so that independent reach has to be worth it.

## Router skills

When user-invoked skills multiply past what you can remember, that piled-up cognitive load is cured by a **router skill**: one user-invoked skill that names the others and when to reach for each, so the human has one skill to remember instead of many. It can only hint, never fire them: user-invoked skills have no description, so nothing but the human can reach them.
