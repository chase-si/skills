---
name: improve-codebase-architecture
description: "Find deepening opportunities using the project's domain glossary and ADRs. Use when improving architecture, consolidating tightly coupled modules, or making a codebase more testable and easier to navigate."
disable-model-invocation: true
---

# Improve Codebase Architecture

Surface architectural friction and propose **deepening opportunities** — refactors that turn shallow modules into deep ones. The aim is testability and AI-navigability.

## Glossary

Use these terms exactly in every suggestion. Consistent language is the point — don't drift into "component," "service," "API," or "boundary." Full definitions in [LANGUAGE.md](LANGUAGE.md).

- **Module** — anything with an interface and an implementation (function, class, package, slice).
- **Interface** — everything a caller must know to use the module: types, invariants, error modes, ordering, config. Not just the type signature.
- **Implementation** — the code inside.
- **Depth** — leverage at the interface: a lot of behaviour behind a small interface. **Deep** = high leverage. **Shallow** = interface nearly as complex as the implementation.
- **Seam** — where an interface lives; a place behaviour can be altered without editing in place. (Use this, not "boundary.")
- **Adapter** — a concrete thing satisfying an interface at a seam.
- **Leverage** — what callers get from depth.
- **Locality** — what maintainers get from depth: change, bugs, knowledge concentrated in one place.

Key principles (see [LANGUAGE.md](LANGUAGE.md) for the full list):

- **Deletion test**: imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep.
- **The interface is the test surface.**
- **One adapter = hypothetical seam. Two adapters = real seam.**

This skill is _informed_ by the project's domain model. The domain language gives names to good seams; ADRs record decisions the skill should not re-litigate.

## Process

### 1. Explore

**Scope before scanning — YAGNI.** Deepening a module pays off when future
changes become easier, so decide where to look before exploring broadly:

- If the user named a module, subsystem, or pain point, use that scope.
- Otherwise, inspect a meaningful stretch of `git log --oneline` and the
  repeatedly changed paths to find current hot spots.
- Widen the scan only when changes are scattered and no useful hot spot emerges.

Read the [domain document rules](../setup-matt-pocock-skills/domain.md) to
resolve the project's glossary and ADRs, including existing `GLOSSARY.md` and
context maps. Read the relevant documents before exploring.

Then delegate a focused exploration using the environment's subagent tools
when available, or explore directly. Follow the scoped friction rather than
rigid heuristics:

- Where does understanding one concept require bouncing between many small modules?
- Where are modules **shallow** — interface nearly as complex as the implementation?
- Where have pure functions been extracted just for testability, but the real bugs hide in how they're called (no **locality**)?
- Where do tightly-coupled modules leak across their seams?
- Which parts of the codebase are untested, or hard to test through their current interface?

Apply the **deletion test** to anything you suspect is shallow: would deleting it concentrate complexity, or just move it? A "yes, concentrates" is the signal you want.

### 2. Present candidates

Present a numbered list of deepening opportunities. For each candidate:

- **Files** — which files/modules are involved
- **Problem** — why the current architecture is causing friction
- **Solution** — plain English description of what would change
- **Benefits** — explained in terms of locality and leverage, and also in how tests would improve

**Use the resolved glossary for domain vocabulary and [LANGUAGE.md](LANGUAGE.md)
for architecture vocabulary.** If the glossary defines "Order," talk about
"the Order intake module" rather than an implementation class name.

**ADR conflicts**: if a candidate contradicts an existing ADR, only surface it when the friction is real enough to warrant revisiting the ADR. Mark it clearly (e.g. _"contradicts ADR-0007 — but worth reopening because…"_). Don't list every theoretical refactor an ADR forbids.

Do NOT propose interfaces yet. Ask the user: "Which of these would you like to explore?"

### 3. Grilling loop

Once the user picks a candidate, read the shared
[interview rules](../../public/grill-me/INTERVIEW.md) and load `domain-modeling`
with the environment's skill-loading tool, or read
[its instructions](../domain-modeling/SKILL.md) when no such tool is available.
Walk the design tree: constraints, dependencies, the deepened module, what sits
behind the seam, and what tests survive.

Side effects happen inline as decisions crystallize:

- **Naming a deepened module after a concept absent from the glossary?** Add it to the resolved glossary using [CONTEXT-FORMAT.md](../domain-modeling/CONTEXT-FORMAT.md). Create a glossary lazily if none exists.
- **Sharpening a fuzzy term during the conversation?** Update that same glossary in place.
- **User rejects the candidate with a load-bearing reason?** Offer an ADR, framed as: _"Want me to record this as an ADR so future architecture reviews don't re-suggest it?"_ Only offer when a future explorer would need the reason; skip ephemeral reasons and self-evident ones. See [ADR-FORMAT.md](../domain-modeling/ADR-FORMAT.md).
- **Want to explore alternative interfaces for the deepened module?** See [INTERFACE-DESIGN.md](INTERFACE-DESIGN.md).
