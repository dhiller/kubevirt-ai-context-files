# Model Advice

## Scope and Evidence

This is local workflow advice for KubeVirt SIG-CI maintenance, not upstream
project policy or automatic model routing. It helps an agent suggest a model to
the user before substantial work or when the work changes materially.

Catalog last checked: **2026-11-25**, through OpenCode's model-discovery tool.
The recommendations below are provisional evaluation candidates, not benchmarked
winners. Catalog presence confirms a selectable model reference, not successful
gateway access, reasoning quality, latency, or actual billing. Model names,
release dates, prices, and context limits do not establish task suitability.

## When and How to Advise

1. Classify the work by reasoning difficulty and consequences, not just skill name.
   Skip model advice for trivial questions and small mechanical operations.
2. Respect an explicit user model choice. Use the active model identity only when
   supplied by reliable session context; do not infer it from prose, response
   quality, or an agent's configured preference.
3. If a different model would materially help, make one short, non-blocking
   suggestion before the substantial work. If the active model is unknown, phrase
   the recommendation conditionally rather than claiming a switch is necessary.
4. Give the exact model reference, task-specific reason, and selection method.
   Verify current catalog availability when uncertain; do not invent a model ID
   or reasoning variant. If unavailable, report that limitation and offer a
   verified alternative rather than silently substituting one.
5. Continue with the current model unless the user asks to pause. Do not switch
   models, change configuration, start paid comparison runs, or launch subagents
   merely because this reference recommends them.
6. Do not repeat advice on every turn or tool call. Revisit it only for a material
   change: conflicting evidence, repeated unsuccessful attempts, cross-component
   diagnosis, or a consequential CI design/review decision.

Example when the active model is unknown:

> Model suggestion: If you are not already using it, consider
> `openai/gpt-6.2-sol#high` via `/models` for this cross-job failure investigation.
> It is a candidate for the causal analysis needed here; I can continue with the
> current model.

Advice never bypasses design/test gates, publication approval, or permission
rules. A stronger model does not authorize retriggers, quarantines, CI changes,
or external mutations.

## Typical Engineering Tasks

Use these assignments as starting points, not rankings. Match reasoning effort
to ambiguity, blast radius, and evidence quality. A small but consequential
decision may need more reasoning than a large mechanical edit.

| Task                                                 | Starting candidate | Escalate when                                                      |
| ---------------------------------------------------- | ------------------ | ------------------------------------------------------------------ |
| File discovery, symbol lookup, evidence collection   | Luna Low           | Evidence is incomplete or conflicting: Terra Medium                |
| Mechanical edits, formatting, established procedures | Terra Medium       | Behavior or multiple boundaries are affected: Sonnet High          |
| Feature implementation, tests, refactoring           | Sonnet High        | Debugging is difficult or design impact is broad: Sol High         |
| Test-failure diagnosis and root-cause analysis       | Sol High           | Evidence is disputed or impact is high: Opus High second opinion   |
| Code review and change-risk analysis                 | Sol High           | Security-sensitive or high-impact: Opus High second opinion        |
| Architecture and design trade-offs                   | Sol High           | Cross-system or disputed design: Opus High second opinion          |
| Documentation, release notes, routine summaries      | Terra Medium       | Sources conflict or causal claims are needed: Sol High             |
| Data extraction and bounded classification           | Luna Low           | Nuanced interpretation is needed: Terra Medium                     |
| Incident, CI, and regression analysis                | Sol High           | High-impact conclusion remains uncertain: Opus High second opinion |

The escalation model is not automatically more correct. Verify claims against
source, tests, logs, and other primary evidence; model agreement is not
independent evidence.

## Starting Model Assignments

Use these short labels in the recommendations; suggest the full reference to users.

- **Luna Low:** `openai/gpt-6-luna#low`.
- **Terra Medium:** `openai/gpt-6.1-terra#medium`.
- **Sol High:** `openai/gpt-6.2-sol#high`.
- **Sonnet Medium:** `anthropic/claude-sonnet-5-5#medium`.
- **Sonnet High:** `anthropic/claude-sonnet-5-5#high`.
- **Opus High:** `anthropic/claude-opus-5-5#high`.
- **Haiku Low:** `anthropic/claude-haiku-5-5#low` (alternative for extraction).

These are hypotheses about a useful division of work, not measured capability
rankings. Prefer a small working set and escalate because the evidence or risk
warrants it, not simply because a higher reasoning setting exists.

- **Discovery:** Luna Low for files, job definitions, existing issues, and check
  states. Consider Terra Medium for ambiguous or incomplete discovery.
- **Routine coordination and summaries:** Terra Medium for established findings;
  consider Sol High for conflicting evidence or causal conclusions.
- **Established validation procedures:** Terra Medium for execution/reporting.
  Reclassify diagnosis or fixes as separate work.
- **Go implementation, tests, and refactoring:** Sonnet High as a starting
  candidate; consider Sol High for difficult debugging.
- **Build/PR diagnosis across logs and cluster artifacts:** Sol High. Seek a
  second opinion before consequential conclusions when uncertainty remains.
- **Flakiness, regressions, and quarantine assessment:** Sol High; consider Opus
  High for disputed, high-impact assessments.
- **Mechanical lane copying and bounded YAML changes:** Terra Medium; consider
  Sonnet High to review the resulting changes.
- **Lane migrations, schedules, and required-check design:** Sol High; consider
  Opus High for a second-opinion review.
- **PR/Jira drafts and incident summaries:** Terra Medium; Sonnet Medium is an
  alternative drafting candidate.

A straightforward compiler diagnostic can be handled as bounded analysis with
Terra Medium; a Prow URL does not automatically require Sol High. Conversely,
short output can still require substantial causal reasoning. Do not ask models
to redo calculations already supplied by CI-health tools. Focus reasoning on
evidence gaps, correlations, alternative explanations, and next investigations.
A second model's agreement is not independent evidence; verify the artifacts.

## Skill Mapping

Use the canonical skills referenced by the parent [AGENTS.md](../../AGENTS.md#shared-skills).
Do not copy their procedures into model prompts or this reference.

- **Luna Low:** `pending-reviews` and bounded evidence gathering. Discovery is not
  a code review or root-cause verdict.
- **Terra Medium:** `go-dev` execution/reporting and check discovery for
  `trigger-pr-jobs`. Fixes and retriggers require their own authorized scope.
- **Terra Medium:** mechanical generation and validation in `copy-lanes`.
  Migration design and review use the higher-risk recommendations above.
- **Sol High:** `analyze-build`, `analyze-pr-builds`, and `analyze-ci-failures`.
  Use simpler analysis for an unambiguous compiler error.
- **Sol High:** `test-failure-rate`, `lane-failure-rate`, `flake-overview`, and
  interpretation of `change-relevance`. Rates and changed-file overlap are
  signals, not proof.

## Primary Sessions, Commands, and Subagents

For OpenCode, use `/models` to select the main session model. Selecting a primary
agent does not by itself replace an existing session's selected model.

When delegation is already authorized, prefer an appropriate configured worker
over asking the user to change the whole parent session. The configuration
verified during this setup used:

- `explore`: Luna Low, for bounded discovery.
- `general`: Terra Medium, for routine multi-step work.
- `/ci-build <prow-build-url>`: a child session using Sol High and `analyze-build`.

These are configuration snapshots, not built-in guarantees. Inspect current
configuration before promising a particular assignment. A subagent's explicit
model otherwise takes precedence over inheritance; a command's explicit model
overrides its selected agent's model. A skill alone does not select a model.

## Refresh and Validate

- Use OpenCode's model-discovery tool, `/models`, or `opencode models` to check
  available references. Inspect the variants before recommending a suffix.
- Use `opencode debug agents` to inspect registered agents, modes, and model
  preferences. Subagent-only profiles are not primary-agent picker entries.
- Inspect the actual child session model when verifying delegation. A successful
  analysis report does not by itself identify the model that produced it.
- Request-level smoke tests can establish gateway access, but spend tokens and
  do not benchmark diagnosis quality. Obtain permission before comparison runs.
- Do not copy catalog prices into this reference; consult current metadata and
  the gateway's billing policy when cost matters. Missing prices do not mean free.
- Revalidate after provider/model changes or observed routing failures. Update
  the check date only when the catalog is actually checked.

Other catalog candidates currently available and worth evaluating, not default
substitutions:

- `anthropic/claude-haiku-5-5`: discovery and extraction.
- `google/gemini-flash-latest`: bounded summaries and extraction.
- `google/gemini-4-neon`: second-opinion analysis.
- `enmaas/rits/zai-org/glm-5-3`: routine coding and summaries.
- `enmaas/rits/deepseek-ai/DeepSeek-V4.1-Flash-1017`: bounded analysis and tool use.

Compare candidates against resolved incidents: a type-check error, an
infrastructure outage, a genuine flake, a mainline regression, and a lane
migration. Track correct diagnosis, cited evidence, tool failures, latency, and
total cost. One easy compiler failure does not establish diagnostic quality.

OpenCode behavior references: [models](https://opencode.ai/docs/models),
[agents](https://opencode.ai/docs/agents), and
[commands](https://opencode.ai/docs/commands). For OpenCode configuration work,
load the `opencode` skill and consult current documentation rather than treating
this snapshot as the configuration specification.
