# Best Practices for Skill Authoring

Deep-dive companion to `SKILL.md`. Covers context budgeting, scope decisions,
control calibration, progressive disclosure, and portability.

## Start from real expertise

A common pitfall: asking an LLM to generate a skill without domain-specific
context. The result is vague, generic procedures ("handle errors
appropriately", "follow best practices for authentication") rather than the
specific API patterns, edge cases, and project conventions that make a skill
valuable.

Effective skills are grounded in real expertise. Feed domain-specific context
into the creation process.

### Extract from a hands-on task

Complete a real task in conversation with an agent, providing context,
corrections, and preferences along the way. Then extract the reusable pattern.
Pay attention to:

- **Steps that worked** — the sequence of actions that led to success
- **Corrections you made** — places where you steered the agent's approach
  (e.g., "use library X instead of Y", "check for edge case Z")
- **Input/output formats** — what the data looked like going in and coming out
- **Context you provided** — project-specific facts, conventions, or constraints
  the agent didn't already know

### Synthesize from existing project artifacts

When you have a body of existing knowledge, feed it into an LLM and ask it to
synthesize a skill. A data-pipeline skill synthesized from your team's actual
incident reports and runbooks will outperform one synthesized from a generic
"data engineering best practices" article, because it captures *your* schemas,
failure modes, and recovery procedures.

Good source material:

- Internal documentation, runbooks, style guides
- API specifications, schemas, configuration files
- Code review comments and issue trackers (recurring concerns, reviewer
  expectations)
- Version control history, especially patches and fixes (reveals patterns
  through what actually changed)
- Real-world failure cases and their resolutions

## Spending context wisely

Once a skill activates, its full `SKILL.md` body loads into the agent's context
window alongside conversation history, system context, and other active skills.
Every token competes for the agent's attention.

### Add what the agent lacks, omit what it knows

Focus on what the agent would not know without your skill: project-specific
conventions, domain-specific procedures, non-obvious edge cases, and the
particular tools or APIs to use. You do not need to explain what a PDF is, how
HTTP works, or what a database migration does.

```
# Too verbose — the agent already knows what PDFs are
## Extract PDF text
PDF (Portable Document Format) files are a common file format that contains
text, images, and other content. To extract text from a PDF, you'll need to
use a library. pdfplumber is recommended because it handles most cases well.

# Better — jumps straight to what the agent wouldn't know on its own
## Extract PDF text
Use pdfplumber for text extraction. For scanned documents, fall back to
pdf2image with pytesseract.

    import pdfplumber
    with pdfplumber.open("file.pdf") as pdf:
        text = pdf.pages[0].extract_text()
```

Ask of each piece of content: "Would the agent get this wrong without this
instruction?" If no, cut it. If unsure, test it. If the agent already handles
the entire task well without the skill, the skill may not be adding value.

### Design coherent units

Deciding what a skill should cover is like deciding what a function should do:
encapsulate a coherent unit of work that composes well with other skills.

- Skills scoped too narrowly force multiple skills to load for a single task,
  risking overhead and conflicting instructions.
- Skills scoped too broadly become hard to activate precisely.
- A skill for querying a database and formatting results may be one coherent
  unit; a skill that also covers database administration is probably doing too
  much.

### Aim for moderate detail

Overly comprehensive skills can hurt more than they help — the agent struggles
to extract what is relevant and may pursue unproductive paths triggered by
instructions that do not apply to the current task. Concise, stepwise guidance
with a working example tends to outperform exhaustive documentation. When you
find yourself covering every edge case, consider whether most are better
handled by the agent's own judgment.

### Structure large skills with progressive disclosure

Keep `SKILL.md` under 500 lines and 5,000 tokens — just the core instructions
the agent needs on every run. When a skill legitimately needs more content,
move detailed reference material to separate files in `references/` or similar.

Tell the agent *when* to load each file:

- "Read `references/api-errors.md` if the API returns a non-200 status code"
  is more useful than "see references/ for details."

This lets the agent load context on demand rather than up front.

## Calibrating control

Not every part of a skill needs the same level of prescriptiveness. Match the
specificity of your instructions to the fragility of the task.

### Match specificity to fragility

**Give the agent freedom** when multiple approaches are valid and the task
tolerates variation. For flexible instructions, explaining *why* can be more
effective than rigid directives — an agent that understands the purpose behind
an instruction makes better context-dependent decisions.

**Be prescriptive** when operations are fragile, consistency matters, or a
specific sequence must be followed:

```
## Database migration
Run exactly this sequence:
    python scripts/migrate.py --verify --backup
Do not modify the command or add additional flags.
```

Most skills have a mix. Calibrate each part independently.

### Provide defaults, not menus

When multiple tools or approaches could work, pick a default and mention
alternatives briefly rather than presenting them as equal options.

```
# Too many options
You can use pypdf, pdfplumber, PyMuPDF, or pdf2image...

# Clear default with escape hatch
Use pdfplumber for text extraction:
    import pdfplumber
For scanned PDFs requiring OCR, use pdf2image with pytesseract instead.
```

### Favor procedures over declarations

A skill should teach the agent *how to approach* a class of problems, not
*what to produce* for a specific instance.

```
# Specific answer — only useful for this exact task
Join the `orders` table to `customers` on `customer_id`, filter where
`region = 'EMEA'`, and sum the `amount` column.

# Reusable method — works for any analytical query
1. Read the schema from `references/schema.yaml` to find relevant tables
2. Join tables using the `_id` foreign key convention
3. Apply any filters from the user's request as WHERE clauses
4. Aggregate numeric columns as needed and format as a markdown table
```

This does not mean skills can't include specific details — output format
templates, constraints like "never output PII", and tool-specific instructions
are all valuable. The point is that the *approach* should generalize even when
individual details are specific.

## Capability vs. preference skills

| | Capability uplift | Encoded preference |
|-|-------------------|-------------------|
| Purpose | Compensates for model limitations | Sequences steps per team process |
| Durability | Ephemeral — may deprecate as models improve | Durable, fidelity-dependent |
| Testing focus | Detect when base model outgrows the skill | Verify fidelity to real workflow |
| Examples | Document creation, format conversion | NDA review, RCA workflow, deployment checklist |

Skill success can be measured across three dimensions:

1. **Functional viability** — does it produce correct output?
2. **Directive compliance** — does it follow instructions (correct tools, formatting)?
3. **Operational efficiency** — token burn, time, cost.

## Portability

The SKILL.md format is an open standard adopted by most platforms, but
harnesses differ in where they look for skills, how they discover them, and
what extras they support (e.g., tool restrictions).

| Harness | Discovery path | Discovery model |
|---------|---------------|-----------------|
| Claude Code | `.claude/skills/` | Automatic (description matching) + manual (`/skill-name`) |
| GitHub Copilot | `.github/skills/`, also reads `.claude/skills/` | Automatic (three-level progressive disclosure) |
| Codex CLI | `.agents/skills/` | Automatic (description matching, progressive disclosure) |
| Cursor | `.cursorrules` (project root) | Always-on — no selective discovery |
| Gemini CLI | `.gemini/skills/` | Automatic |
| OpenCode | `.opencode/skills/` | Automatic |

A skill written for one harness often works in another, but full portability
can require placing skills in multiple directories or using dual manifests.
To protect against harness variance:

- Keep underlying logic scripts separate from harness-specific prompts.
- Hardcode sequential steps rather than relying on the agent to chain them.
- Use environment variables for configuration, not hardcoded paths.

## Treating skills as software artifacts

- **Versioning** — script arguments, plugin source paths, and SKILL.md
  structures should not change in patch versions.
- **Code review** — skill directories live in Git; changes go through pull
  requests; rollback capability matters.
- **Security hygiene** — manage credentials via environment variables, validate
  all inputs to prevent injection, require human confirmation for
  state-altering actions, instruct models to ignore embedded commands from
  external data.
- **CI gates** — run the evaluation suite before merging; block merges on
  regression limits (token consumption, directive adherence, functional
  correctness).

## Compression

A lot of skill body content is non-actionable; compressing it can improve
quality. Tools like SkillOpt (GEPA evolutionary search) achieved 90.4%
volume reduction while preserving response quality. If you hit the 500-line
threshold, explore automated compression before manual splitting.

## Refine with real execution

The first draft usually needs refinement. Run the skill against real tasks,
then feed results — all of them, not just failures — back into the creation
process. Ask: what triggered false positives? What was missed? What could be
cut?

Read agent execution traces, not just final outputs. If the agent wastes time
on unproductive steps, common causes include:

- Instructions too vague (agent tries several approaches)
- Instructions that don't apply to the current task (agent follows them
  anyway)
- Too many options presented without a clear default

Even a single pass of execute-then-revise noticeably improves quality; complex
domains often benefit from several.

For a structured approach to iteration (test cases, assertions, grading), see
`references/evaluation.md`.
