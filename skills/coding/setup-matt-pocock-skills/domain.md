# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Resolve the domain documents

Use the repo's `docs/agents/domain.md` and navigation in `AGENTS.md` or
`CLAUDE.md` to find the configured glossary and context map. An explicit
project configuration takes precedence over filename defaults, including
existing projects configured to use `GLOSSARY.md` or `GLOSSARY-MAP.md`.

Without an explicit configuration:

1. Read an existing `CONTEXT-MAP.md` or compatible `GLOSSARY-MAP.md` first. Follow
   its actual links to the relevant contexts, even when their filenames mix
   old and new conventions. Do not infer a single context from a root glossary
   when a context map exists.
2. With no map, read the root `CONTEXT.md`, falling back to an existing `GLOSSARY.md`.
3. If competing maps or glossary files describe the same context, consult the
   repo's navigation and their contents to establish the authoritative file.
   Report material conflicts. Resolve any remaining ambiguity with the user
   before writing, rather than silently picking one or synchronizing both.

Read `docs/adr/` for decisions relevant to the work. In a multi-context repo,
also read the relevant context's ADR directory at the location the repo uses.

Read and update the resolved glossary in place. Keep existing filenames and
links; do not rename files or create a second glossary as part of ordinary
feature work. For a new project with no domain documents, use `CONTEXT.md`
and, only when multiple contexts are needed, `CONTEXT-MAP.md`. Create them
lazily when a term or relationship is actually resolved.

If any of these files don't exist, **proceed silently**. Don't flag their absence; don't suggest creating them upfront. The producer skill (`/grill-with-docs`) creates them lazily when terms or decisions actually get resolved.

## File structure

Single-context repo (most repos):

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Multi-context repo (default filenames shown; configured alternatives remain supported):

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal,
a hypothesis, a test name), use the term as defined in the resolved glossary.
Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, that's a signal — either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/grill-with-docs`).

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding:

> _Contradicts ADR-0007 (event-sourced orders) — but worth reopening because…_
