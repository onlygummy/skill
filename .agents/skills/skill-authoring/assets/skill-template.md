---
name: my-skill
description: |
  One-line summary of what this skill does.
  Use when [trigger condition 1], [trigger condition 2], or [trigger condition 3].
  Do NOT use this skill for [out-of-scope case].
---

# My Skill

## Overview

Brief description of the skill's purpose and the problem it solves.

## When to use this skill

- Trigger condition 1
- Trigger condition 2
- Trigger condition 3

## Prerequisites

- Required tool or environment
- Required credentials (via environment variables)

## Workflow

### Step 1: [First step]

Description of what to do.

### Step 2: [Second step]

Description of what to do.

### Step 3: [Third step]

Description of what to do.

## Gotchas

- Environment-specific fact that defies reasonable assumptions.
- Another non-obvious correction the agent needs.

## Output format

Use this template:

```
# [Title]

## Section 1
[Content]

## Section 2
[Content]
```

## Validation

1. Make your edits.
2. Run validation: `python scripts/validate.py output/`
3. If validation fails, review the error, fix, and re-run.
4. Only proceed when validation passes.

## Resources

- `references/advanced-usage.md` — detailed reference (load when needed)
- `scripts/helper.py` — reusable utility script

## References

- [Agent Skills specification](https://agentskills.io/specification)
- [Best practices](https://agentskills.io/skill-creation/best-practices)
