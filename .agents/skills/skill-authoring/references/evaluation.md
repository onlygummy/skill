# Evaluating Skill Output Quality

Deep-dive companion to `SKILL.md`. Covers eval design, testing strategies,
benchmarking, description optimization, and CI integration.

## Why evaluate

Testing turns a skill that *seems* to work into one you *know* works. Evals
also detect:

- **Regressions** — a skill that worked last month may behave differently
  after a model or infrastructure update.
- **Model outgrowth** — when the base model passes your evals *without* the
  skill loaded, the skill's techniques may have been incorporated into the
  model's default behavior. The skill is not broken; it is no longer necessary.

## Three evaluation dimensions

Skill success can be measured across:

1. **Functional viability** — does it produce correct output? (LLM-as-judge,
   script checking)
2. **Directive compliance** — does it follow instructions? (correct tools,
   formatting)
3. **Operational efficiency** — token burn, time, cost, optimization

## Building an eval dataset

A good test case has three components:

1. A realistic, context-rich user prompt
2. A clear description of expected output
3. Any input files needed for execution

Start lean: 2-3 varied prompts covering casual language, precise language, and
boundary conditions. Expand as you learn where the skill breaks.

### Example: RCA evaluation dataset

Include actual bugs, their root causes, critical findings, and red herrings.
This tests not only whether the skill finds the right answer, but whether it
avoids the wrong ones.

## Three test types

### 1. Triggering tests

Goal: ensure the skill loads at the right times.

Should trigger:

- "Help me set up a new ProjectHub workspace"
- "I need to create a project in ProjectHub"
- "Initialize a ProjectHub project for Q4 planning"

Should NOT trigger:

- "What's the weather in San Francisco?"
- "Help me write Python code"
- "Create a spreadsheet" (unless the skill handles sheets)

Test obvious tasks, paraphrased requests, and unrelated topics.

### 2. Functional tests

Goal: verify the skill produces correct outputs.

- Valid outputs generated
- API calls succeed
- Error handling works
- Edge cases covered

Example:

```
Test: Create project with 5 tasks
Given: Project name "Q4 Planning", 5 task descriptions
When: Skill executes workflow
Then:
  - Project created in ProjectHub
  - 5 tasks created with correct properties
  - All tasks linked to project
  - No API errors
```

### 3. Performance comparison

Goal: prove the skill improves results vs. baseline.

Baseline (without skill):

- User provides instructions each time
- 15 back-and-forth messages
- 3 failed API calls requiring retry
- 12,000 tokens consumed

With skill:

- Automatic workflow execution
- 2 clarifying questions only
- 0 failed API calls
- 6,000 tokens consumed

## Benchmark mode

Run a standardized assessment using your evals. Track:

- Eval pass rate
- Elapsed time
- Token usage

Run after model updates or as you iterate on the skill itself. Store evals and
results locally, integrate with a dashboard, or plug into CI.

## Multi-agent evaluation

Running evals sequentially can be slow, and accumulating context can bleed
between test runs. Use independent agents to run evals in parallel — each in
a clean context with its own token and timing metrics. Faster results, no
cross-contamination.

## Comparator agents (A/B)

For A/B comparisons (two skill versions, or skill vs. no skill), use
comparator agents that judge outputs without knowing which is which. This
tells you whether a change actually helped.

## Description optimization

As skill count grows, description precision becomes critical: too broad
causes false triggers, too narrow causes missed triggers.

Optimization loop:

1. Analyze the current description against sample prompts.
2. Generate trigger / no-trigger queries.
3. Split 60/40 train/test.
4. Iteratively improve trigger accuracy.
5. Suggest edits that cut both false positives and false negatives.

Run across your skills — in one reported run, triggering improved on 5 of 6
public document-creation skills.

## Evaluation tooling

Several tools can help build and run evaluations:

- **skill-creator** (Anthropic) — generates test cases, runs with-skill vs
  without-skill in parallel, grades via LLM judge, aggregates benchmark
  statistics (mean/stddev for pass rate, tokens, time), iterates based on
  feedback. Includes a description optimization loop.
- **Feedback capture** — collects freeform user commentary after primary skill
  use, generates summaries, categorizes feedback, stores for analysis. Surfaces
  real-world failure modes that synthetic test cases miss.
- **Structured annotation** — walks users through a questionnaire to produce
  labeled ground truth data.
- **Observability / tracing** — chat histories, execution timelines,
  step-by-step audit trails. Surfaces token cost, reasoning drift, and
  tool-calling precision.

## Continuous integration for skills

To maintain stability, run your evaluation suite before any version upgrade:

- **Automated regression testing** — configure CI to initialize the runtime
  and run evaluations before merging updates.
- **Impact analysis** — compare across token expenditure, directive
  adherence, and functional correctness.
- **Quality gates** — block merges based on regression limits, particularly
  to catch silent increases in token consumption.

## Iteration based on feedback

Skills are living documents. Plan to iterate based on signals:

Under-triggering signals:

- Skill does not load when it should
- Users manually enabling it
- Support questions about when to use it

Solution: add more detail and nuance to the description, including keywords
(especially technical terms).

Over-triggering signals:

- Skill loads for irrelevant queries
- Users disabling it

Solution: add negative triggers, be more specific.

Execution issues:

- Inconsistent results
- API call failures
- User corrections needed

Solution: improve instructions, add error handling.

## Iteration tip

Iterate on a single challenging task until the agent succeeds, then extract
the winning approach into a skill. This leverages the agent's in-context
learning and provides faster signal than broad testing. Once you have a
working foundation, expand to multiple test cases for coverage.
