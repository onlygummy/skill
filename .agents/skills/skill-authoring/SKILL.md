---
name: skill-authoring
description: |
  Author, structure, and refine AI agent skills (SKILL.md + references/scripts/assets).
  Use when creating a new skill, improving an existing skill's description, scope,
  or progressive disclosure, or when reviewing a skill for best practices. Covers
  skill types, authoring workflow, context budgeting, control calibration, patterns
  (gotchas, templates, validation loops, plan-validate-execute), evaluation, and
  portability across harnesses.
license: MIT
metadata:
  author: onlygummy
  version: "1.0.0"
---

# Skill Authoring

This skill teaches how to create high-quality AI agent skills. A skill is a
self-contained unit of capability: a folder containing a `SKILL.md` file plus
optional `references/`, `scripts/`, and `assets/`. The agent discovers skills
via their metadata and loads the full instructions only when the task matches.

## When to use this skill

- Creating a new skill from scratch
- Refactoring a verbose or under-triggering skill
- Reviewing a skill against best practices
- Deciding scope, granularity, or split between scripts and LLM reasoning
- Optimizing a skill's `description` for reliable triggering
- Setting up evaluation (evals, benchmarks) for a skill

## Skill types

Two kinds of skills need different testing and investment:

| Type | Purpose | Durability | Testing focus |
|------|---------|------------|---------------|
| **Capability uplift** | Compensates for what the base model can't do consistently (tool calling, format conversion, domain procedures) | Ephemeral — may deprecate as models improve | Detect when the base model outgrows the skill |
| **Encoded preference** | Sequences steps per your team's process (NDA review, weekly updates, RCA workflow) | Durable, but only as valuable as fidelity to the real workflow | Verify fidelity to the actual workflow |

Decide which type you are building before investing heavily. Process knowledge
(what to check, in what order, why) is harder to replace than tooling.

## Authoring workflow

### 1. Define scope and type

- What single coherent unit of work does this skill encapsulate?
- Is it capability uplift or encoded preference?
- Granularity: aim for 1–3 skills per task. Too broad degrades performance;
  too narrow creates overhead and conflicting instructions.

### 2. Draft SKILL.md

Start from real expertise, not LLM imagination:

- **Extract from a hands-on task** — complete a real task with an agent, noting
  steps that worked, corrections you made, input/output formats, and
  project-specific context.
- **Synthesize from project artifacts** — runbooks, API specs, code review
  comments, version control history, incident reports.

Every instruction must answer: "Would the agent get this wrong without this?"

### 3. Structure with progressive disclosure

Keep `SKILL.md` under **500 lines / 5,000 tokens**. Move detailed reference
material to `references/` and tell the agent *when* to load each file.

```
my-skill/
├── SKILL.md          ← core instructions (Level 2)
├── references/       ← Level 3, loaded on demand
├── scripts/          ← executable code, no context cost
└── assets/           ← templates, images, loaded on demand
```

Three-level loading:

| Level | Loaded when | Token cost | Content |
|-------|-------------|------------|---------|
| 1. Metadata | Session start | ~100 tokens | `name` + `description` |
| 2. Instructions | Skill triggers | <5,000 tokens | SKILL.md body |
| 3. Resources | Needed at runtime | Variable | references/, scripts/, assets/ |

### 4. Add high-value patterns

- **Gotchas** — environment-specific facts that defy reasonable assumptions
- **Templates** — concrete output structures (more reliable than prose)
- **Checklists** — for multi-step workflows with dependencies
- **Validation loops** — do the work → validate → fix → repeat
- **Plan-validate-execute** — for batch/destructive operations

See `references/patterns.md` for full patterns and examples.

### 5. Test and iterate

- **Triggering tests** — fires on obvious + paraphrased requests; not on unrelated topics
- **Functional tests** — correct outputs, successful API calls, error handling
- **Performance comparison** — tokens, messages, failed calls vs. baseline

Run with-skill vs. without-skill. Iterate on a single challenging task first,
then expand to multiple test cases. See `references/evaluation.md`.

## Core principles

### Add what the agent lacks, omit what it knows

Focus on project-specific conventions, domain procedures, non-obvious edge cases,
and the specific tools/APIs to use. Do not explain what PDFs are, how HTTP
works, or what a database migration is.

Ask of every line: "Would the agent get this wrong without this instruction?"
If no, cut it. If unsure, test it.

### Design coherent units

Like a function: encapsulate a coherent unit of work that composes well with
other skills. Skills scoped too narrowly force multiple skills to load for one
task (overhead + conflicting instructions). Skills scoped too broadly are hard
to activate precisely.

### Aim for moderate detail

Overly comprehensive skills hurt — the agent struggles to extract what's
relevant and may pursue unproductive paths. Concise, stepwise guidance with a
working example outperforms exhaustive documentation.

### Calibrate control to fragility

- **Freedom** when multiple approaches are valid and variation is tolerated.
  Explain *why* rather than issuing rigid directives.
- **Prescriptive** when operations are fragile, consistency matters, or a
  specific sequence must be followed.

Most skills mix both. Calibrate each part independently.

### Provide defaults, not menus

When multiple tools could work, pick a default and mention alternatives briefly
as escape hatches — not as equal options.

### Favor procedures over declarations

Teach *how to approach* a class of problems, not *what to produce* for a
specific instance. The approach should generalize even when individual details
are specific. Output format templates and constraints ("never output PII") are
fine — but the method must generalize.

### Scripts vs. LLM reasoning

The most impactful design decision:

- **Scripts** — cheap, transparent, testable. Use for data fetching, formatting,
  validation. Code runs without loading into context (only output counts).
- **SKILL.md instructions** — subjective analysis, advanced reasoning, too many
  edge cases to enumerate.

Hybrid is best for production: scripts for the mechanical layer, SKILL.md for
the reasoning layer. Switching inference-driven fetching to script-based API
calls cut cost 26% in Red Hat's RCA experiments.

### Handle errors in scripts

Do not let a script fail and expect the agent to recover. Handle errors inside
the script and return actionable messages (e.g., "Field 'signature_date' not
found — available: customer_name, order_total").

## Patterns for effective instructions

See `references/patterns.md` for full patterns with examples:

- Gotchas sections
- Templates for output format
- Checklists for multi-step workflows
- Validation loops
- Plan-validate-execute
- Bundling reusable scripts
- Five workflow patterns (sequential, multi-MCP, iterative, context-aware,
  domain-specific)

## Evaluation

See `references/evaluation.md` for full evaluation guidance:

- Three dimensions: functional viability, directive compliance, operational
  efficiency
- Building an eval dataset (realistic prompts, expected output, input files)
- Triggering / functional / performance tests
- Benchmark mode (pass rate, time, tokens)
- Multi-agent parallel evals + comparator agents (A/B)
- Description optimization loop
- CI gates and regression testing

## Portability

The SKILL.md format is an open standard, but harnesses differ in discovery
paths and extras (tool restrictions). Keep logic scripts decoupled from
harness-specific prompts.

| Harness | Discovery path | Model |
|---------|---------------|-------|
| Claude Code | `.claude/skills/` | Automatic + manual `/skill-name` |
| GitHub Copilot | `.github/skills/` (also reads `.claude/skills/`) | Progressive disclosure |
| Codex CLI | `.agents/skills/` | Automatic + progressive disclosure |
| Cursor | `.cursorrules` (project root) | Always-on |
| Gemini CLI | `.gemini/skills/` | Automatic |
| OpenCode | `.opencode/skills/` | Automatic |

A skill written for one harness often works in another, but full portability
may require placing skills in multiple directories or using dual manifests.

## Security and hygiene

- Skills can run code on the user's machine — review `SKILL.md` and `scripts/`
  before installing anything from an untrusted source.
- Manage credentials via environment variables, never hardcode.
- Validate all inputs to prevent injection.
- Require human confirmation for state-altering actions.
- Instruct the model to ignore embedded commands in external data.
- Version skills in Git; changes go through pull requests; run evals before
  promoting a new version.

## Resources

- Agent Skills specification: https://agentskills.io/specification
- Best practices (official): https://agentskills.io/skill-creation/best-practices
- Anthropic skill-creator: https://github.com/anthropics/skills/tree/main/skills/skill-creator
- Anthropic guide (PDF): https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf
- Red Hat best practices: https://next.redhat.com/2026/07/28/building-skills-for-ai-agents-pitfalls-and-best-practices/
- SkillOpt (compression): https://github.com/AIMikav/skillopt.git

## Quick start

```bash
mkdir -p .opencode/skills/my-skill
# create .opencode/skills/my-skill/SKILL.md with frontmatter + instructions
# add references/, scripts/, assets/ as needed
```

Or use the template at `assets/skill-template.md`.
