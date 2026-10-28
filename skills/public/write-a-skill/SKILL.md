---
name: write-a-skill
description: Create new agent skills with proper structure, progressive disclosure, and bundled resources. Use when user wants to create, write, or build a new skill.
disable-model-invocation: true
---

# Writing Skills

## Process

1. **Gather requirements** - use the conversation and available references
   first. Ask only for material gaps:
   - What task/domain does the skill cover?
   - What specific use cases should it handle?
   - Does it need executable scripts or just instructions?
   - Any reference materials to include?

2. **Draft the skill** - create:
   - SKILL.md with concise instructions
   - Reference files for substantial conditional detail or shared procedures
   - Utility scripts if deterministic operations needed

3. **Review with user** - present draft and ask:
   - Does this cover your use cases?
   - Anything missing or unclear?
   - Should any section be more/less detailed?

4. **Audit predictability** - load `writing-great-skills` with the environment's
   skill-loading tool, or read
   [its instructions](../writing-great-skills/SKILL.md) when no such tool is
   available, before finalizing.
   Check invocation choice, completion criteria, progressive disclosure,
   duplicated meanings, no-op instructions, sediment, and negation.

## Skill Structure

```
skill-name/
├── SKILL.md           # Main instructions (required)
├── REFERENCE.md       # Detailed docs (if needed)
├── EXAMPLES.md        # Usage examples (if needed)
└── scripts/           # Utility scripts (if needed)
    └── helper.js
```

## SKILL.md Template

```md
---
name: skill-name
description: Brief description of capability. Use when [specific triggers].
---

# Skill Name

## Quick start

[Minimal working example]

## Workflows

[Step-by-step processes with checklists for complex tasks]

## Advanced features

[Link to separate files: See [REFERENCE.md](REFERENCE.md)]
```

## Description Requirements

For model-invoked skills, the name and description guide discovery before the
body is read. Explicit-only entrypoints follow the invocation rules in
[SKILL-MECHANICS.md](../writing-great-skills/SKILL-MECHANICS.md). Preserve an
existing invocation policy unless the user asks to change it.

**Goal**: Give your agent just enough info to know:

1. What capability this skill provides
2. When/why to trigger it (specific keywords, contexts, file types)

**Format**:

- Max 1024 chars
- Write in third person
- First sentence: what it does
- Second sentence: "Use when [specific triggers]"

**Good example**:

```
Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when user mentions PDFs, forms, or document extraction.
```

**Bad example**:

```
Helps with documents.
```

The bad example gives your agent no way to distinguish this from other document skills.

## When to Add Scripts

Add utility scripts when:

- Operation is deterministic (validation, formatting)
- Same code would be generated repeatedly
- Errors need explicit handling

Scripts save tokens and improve reliability vs generated code.

## When to Split Files

Split into separate files when:

- Shared or conditional guidance obscures the main workflow
- Content has distinct domains (finance vs sales schemas)
- Advanced features are rarely needed

## Review Checklist

After drafting, verify:

- [ ] Description includes triggers ("Use when...")
- [ ] Main workflow remains clear; conditional detail has useful pointers
- [ ] Time-sensitive claims are verified or linked to an authoritative source
- [ ] Consistent terminology
- [ ] Examples clarify a real decision or workflow
- [ ] Required references exist and state when to read them
