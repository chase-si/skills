---
name: domain-modeling
description: "Sharpen project terminology and record architectural decisions. Use when discussing domain language or writing or editing a project glossary or ADR."
---

# Domain Modeling

Actively build and sharpen the project's domain model as you design. Challenge
terms, invent edge-case scenarios, and write the glossary and decisions down
the moment they crystallise. Reading an existing glossary for vocabulary alone
does not require this skill.

## File structure

First read the [domain document rules](../setup-matt-pocock-skills/domain.md)
to resolve the project's configured glossary and map, including existing
`GLOSSARY.md` / `GLOSSARY-MAP.md` projects. The examples below use the defaults for new
projects; keep existing projects' filenames and update the resolved file.

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

If a context map exists, follow its links to the relevant contexts:

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

Create files lazily. When the first term is resolved, create a missing glossary
at the path selected by the repo's configuration or context map. Use root
`CONTEXT.md` only when no layout exists. Create the relevant ADR directory
when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the resolved glossary, call it out
immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y.
Which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update the glossary inline

When a term is resolved, update the resolved glossary right there. Capture terms
as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

Keep the glossary focused on domain terms and relationships. Put requirements
in the PRD and implementation decisions in the appropriate design doc or ADR.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md).
