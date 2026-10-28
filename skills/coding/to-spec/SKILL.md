---
name: to-spec
description: "Compatibility entry for to-prd. Synthesize the current context into a PRD with stable acceptance IDs and verification decisions."
disable-model-invocation: true
---

# To Spec

Read [../to-prd/SKILL.md](../to-prd/SKILL.md) in full and follow it as
this entrypoint's shared workflow. This loads the reference within the current
user-authorized invocation; it does not call another user-only skill.

`/to-prd` is the primary local name. Preserve its templates, labels,
acceptance traceability, verification rules, and publishing behavior when the
user enters through `/to-spec`.
