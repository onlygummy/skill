# Patterns for Effective Skill Instructions

Deep-dive companion to `SKILL.md`. Reusable techniques for structuring skill
content. Use the ones that fit your task — not every skill needs all of them.

## Gotchas sections

The highest-value content in many skills is a list of gotchas —
environment-specific facts that defy reasonable assumptions. These are not
general advice ("handle errors appropriately") but concrete corrections to
mistakes the agent will make without being told otherwise.

```
## Gotchas

- The `users` table uses soft deletes. Queries must include
  `WHERE deleted_at IS NULL` or results will include deactivated accounts.
- The user ID is `user_id` in the database, `uid` in the auth service, and
  `accountId` in the billing API. All three refer to the same value.
- The `/health` endpoint returns 200 as long as the web server is running,
  even if the database connection is down. Use `/ready` to check full
  service health.
```

Keep gotchas in `SKILL.md` where the agent reads them before encountering the
situation. A separate reference file works if you tell the agent when to load
it, but for non-obvious issues the agent may not recognize the trigger.

When an agent makes a mistake you have to correct, add the correction to the
gotchas section. This is one of the most direct ways to improve a skill
iteratively.

## Templates for output format

When you need the agent to produce output in a specific format, provide a
template. This is more reliable than describing the format in prose, because
agents pattern-match well against concrete structures. Short templates can live
inline in `SKILL.md`; for longer templates, or templates only needed in
certain cases, store them in `assets/` and reference them from `SKILL.md` so
they only load when needed.

```
## Report structure

Use this template, adapting sections as needed for the specific analysis:

# [Analysis Title]

## Executive summary
[One-paragraph overview of key findings]

## Key findings
- Finding 1 with supporting data
- Finding 2 with supporting data

## Recommendations
1. Specific actionable recommendation
2. Specific actionable recommendation
```

## Checklists for multi-step workflows

An explicit checklist helps the agent track progress and avoid skipping steps,
especially when steps have dependencies or validation gates.

```
## Form processing workflow

Progress:
- [ ] Step 1: Analyze the form (run `scripts/analyze_form.py`)
- [ ] Step 2: Create field mapping (edit `fields.json`)
- [ ] Step 3: Validate mapping (run `scripts/validate_fields.py`)
- [ ] Step 4: Fill the form (run `scripts/fill_form.py`)
- [ ] Step 5: Verify output (run `scripts/verify_output.py`)
```

## Validation loops

Instruct the agent to validate its own work before moving on. The pattern is:
do the work, run a validator (a script, a reference checklist, or a
self-check), fix any issues, and repeat until validation passes.

```
## Editing workflow

1. Make your edits
2. Run validation: `python scripts/validate.py output/`
3. If validation fails:
   - Review the error message
   - Fix the issues
   - Run validation again
4. Only proceed when validation passes
```

A reference document can also serve as the "validator" — instruct the agent to
check its work against the reference before finalizing.

## Plan-validate-execute

For batch or destructive operations, have the agent create an intermediate plan
in a structured format, validate it against a source of truth, and only then
execute.

```
## PDF form filling

1. Extract form fields: `python scripts/analyze_form.py input.pdf`
   -> `form_fields.json` (lists every field name, type, required flag)
2. Create `field_values.json` mapping each field name to its intended value
3. Validate: `python scripts/validate_fields.py form_fields.json field_values.json`
   (checks that every field name exists, types are compatible, required fields
   present)
4. If validation fails, revise `field_values.json` and re-validate
5. Fill the form: `python scripts/fill_form.py input.pdf field_values.json output.pdf`
```

The key ingredient is step 3: a validation script that checks the plan against
the source of truth. Errors like "Field 'signature_date' not found — available
fields: customer_name, order_total, signature_date_signed" give the agent
enough information to self-correct.

## Bundling reusable scripts

When iterating on a skill, compare the agent's execution traces across test
cases. If you notice the agent independently reinventing the same logic each
run — building charts, parsing a specific format, validating output — that is
a signal to write a tested script once and bundle it in `scripts/`.

Designing scripts:

- Scripts must be self-contained or clearly declare dependencies.
- Document usage in SKILL.md or a referenced file.
- Handle errors inside the script and return actionable messages.
- Provide constants/config rather than magic numbers.
- Test scripts independently before bundling.

## Five workflow patterns

These patterns emerged from skills created by early adopters and internal
teams. They are common approaches that work well, not prescriptive templates.

### Pattern 1: Sequential workflow orchestration

Use when users need multi-step processes in a specific order.

```
## Workflow: Onboard New Customer

### Step 1: Create Account
Call MCP tool: `create_customer`
Parameters: name, email, company

### Step 2: Setup Payment
Call MCP tool: `setup_payment_method`
Wait for: payment method verification

### Step 3: Create Subscription
Call MCP tool: `create_subscription`
Parameters: plan_id, customer_id (from Step 1)

### Step 4: Send Welcome Email
Call MCP tool: `send_email`
Template: welcome_email_template
```

Key techniques: explicit step ordering, dependencies between steps, validation
at each stage, rollback instructions for failures.

### Pattern 2: Multi-MCP coordination

Use when workflows span multiple services.

```
## Design-to-development handoff

### Phase 1: Design Export (Figma MCP)
1. Export design assets from Figma
2. Generate design specifications
3. Create asset manifest

### Phase 2: Asset Storage (Drive MCP)
1. Create project folder in Drive
2. Upload all assets
3. Generate shareable links

### Phase 3: Task Creation (Linear MCP)
1. Create development tasks
2. Attach asset links to tasks
3. Assign to engineering team

### Phase 4: Notification (Slack MCP)
1. Post handoff summary to #engineering
2. Include asset links and task references
```

Key techniques: clear phase separation, data passing between MCPs, validation
before moving to next phase, centralized error handling.

### Pattern 3: Iterative refinement

Use when output quality improves with iteration.

```
## Iterative Report Creation

### Initial Draft
1. Fetch data via MCP
2. Generate first draft report
3. Save to temporary file

### Quality Check
1. Run validation script: `scripts/check_report.py`
2. Identify issues: missing sections, inconsistent formatting, data errors

### Refinement Loop
1. Address each identified issue
2. Regenerate affected sections
3. Re-validate
4. Repeat until quality threshold met

### Finalization
1. Apply final formatting
2. Generate summary
3. Save final version
```

Key techniques: explicit quality criteria, iterative improvement, validation
scripts, know when to stop iterating.

### Pattern 4: Context-aware tool selection

Use when the same outcome needs different tools depending on context.

```
## Smart File Storage

### Decision Tree
1. Check file type and size
2. Determine best storage location:
   - Large files (>10MB): use cloud storage MCP
   - Collaborative docs: use Notion/Docs MCP
   - Code files: use GitHub MCP
   - Temporary files: use local storage

### Execute Storage
Based on decision: call appropriate MCP tool, apply service-specific
metadata, generate access link.

### Provide Context to User
Explain why that storage was chosen.
```

Key techniques: clear decision criteria, fallback options, transparency about
choices.

### Pattern 5: Domain-specific intelligence

Use when the skill adds specialized knowledge beyond tool access.

```
## Payment Processing with Compliance

### Before Processing (Compliance Check)
1. Fetch transaction details via MCP
2. Apply compliance rules:
   - Check sanctions lists
   - Verify jurisdiction allowances
   - Assess risk level
3. Document compliance decision

### Processing
IF compliance passed:
  - Call payment processing MCP tool
  - Apply appropriate fraud checks
  - Process transaction
ELSE:
  - Flag for review
  - Create compliance case

### Audit Trail
- Log all compliance checks
- Record processing decisions
- Generate audit report
```

Key techniques: domain expertise embedded in logic, compliance before action,
comprehensive documentation, clear governance.

## Common pitfalls

- **Asking an LLM to generate a skill without domain context** — produces
  vague, generic procedures.
- **Explaining what the agent already knows** — wastes context budget.
- **Too many options without a default** — agent hesitates or picks poorly.
- **Nested references** (A -> B -> C) — keep references one level deep from
  SKILL.md.
- **Letting scripts fail silently** — handle errors inside scripts.
- **SKILL.md over 500 lines** — split optional content into references/.
- **Ungated Level-3 resources** — can inject tens of thousands of tokens per
  invocation.
- **Skill that only works when force-loaded** — unlikely to work in production.
- **No evaluation** — cannot detect regressions or when a model outgrows the
  skill.
- **Hardcoding paths and credentials** — breaks portability and security.
- **Ignoring execution traces** — final outputs hide wasted steps.

## Triggering reliability tips

- Write the `description` like a search-optimized abstract: what the skill
  does, what inputs it needs, what outputs it produces.
- Add negative constraints: "Do NOT use this skill for X" to clarify
  boundaries.
- Use a lightweight router skill to hand off tasks in crowded environments.
- Namespace skills by domain to reduce ambiguity.
- Test with casual, precise, and boundary phrasings.

## Portability tips

- Decouple logic scripts from harness-specific prompts and configuration.
- Hardcode sequential steps rather than relying on the agent to chain them.
- A skill may perform differently across runtimes depending on how each
  harness implements skill retrieval and injection.
- Keep environment variables in the working directory's config, not in the
  plugin directory.

## Refinement loop

1. Run the skill against real tasks (with-skill and without-skill).
2. Read execution traces, not just final outputs.
3. Note false positives, misses, wasted steps, and unnecessary content.
4. Update SKILL.md: add gotchas, tighten instructions, remove dead content.
5. Re-run evals; repeat until stable.

Even a single pass of execute-then-revise noticeably improves quality. Complex
domains often benefit from several.
