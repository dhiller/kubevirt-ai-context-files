# Design-First Development Workflow

## Purpose and Scope

Review intent, behavioral contracts, and evidence before implementation. Use
explicit checkpoints to preserve architectural control while allowing autonomous
work within each approved phase. Documentation supplements, not replaces,
targeted code review and validation.

Classify the task before editing and briefly explain the classification:

- **Lightweight path:** typos, formatting, mechanical changes, and clearly
  behavior-preserving edits. State the scope and validation plan, then proceed
  within the user's authorization. No separate design or test approval is needed.
- **Gated path:** non-trivial behavioral changes, including changes to public
  contracts, controller reconciliation, concurrency, retries, persistence,
  security, or CI orchestration. Follow the phases below.
- If the classification is uncertain, explain why and ask before production
  edits. Configuration and scripts that change runtime or CI behavior count as
  implementation, not merely documentation.

Follow applicable repository contributor requirements. This is local agent
workflow, not a claim that a project has adopted these approval requirements.
Skills and automated workflows must honor these gates; invoking a skill does not
authorize skipping a checkpoint.

## Phase 1: Investigate and Propose — Stop for Approval

Inspect source and existing documentation, run existing tests, and perform safe,
local reproductions. Do not change production behavior or perform unapproved
external mutations. Report inconclusive evidence rather than inventing certainty.

Present a compact design brief:

1. **Problem and evidence:** expected versus observed behavior, supporting source
   or test references, and facts distinguished from hypotheses.
2. **Behavioral contract:** relevant inputs, outputs, errors, invariants, and edge
   cases. Include compatibility, cancellation, ownership, ordering, idempotency,
   or concurrency guarantees where applicable.
3. **Proposed approach:** affected boundaries and explicit non-goals.
4. **Alternatives and trade-offs:** include the smallest viable fix; explain why
   more extensive changes are necessary if proposed.
5. **Acceptance criteria:** observable outcomes and the tests intended to establish
   them. Do not define success solely as implementation details matching a plan.
6. **Risks:** compatibility, rollout, recovery, resource use, and operational
   implications where relevant.

Use the artifact rules below to decide whether signatures, an ADR, or a diagram
are needed. End by requesting approval for test generation only, and wait. Do not
include an unsolicited completed implementation alongside the proposal.

## Phase 2: Tests — Stop for Approval

- Write tests against the approved behavior using repository conventions. Test
  observable contracts rather than duplicating the proposed production logic.
- Demonstrate the regression failure where feasible. Distinguish the expected
  behavioral failure from compilation errors, environment failures, and unrelated
  failures. For new behavior, show which acceptance criteria are not yet met.
- Do not add production implementation or stubs merely to make tests run. If the
  proposed API does not exist yet, report that limitation and the resulting
  compilation failure honestly.
- If automated tests are impractical, present the reason and concrete alternative
  checks for explicit approval; do not silently skip the test gate.
- If testing invalidates the contract or requires a material design change,
  return to Phase 1.

Present the tests, their mapping to acceptance criteria, and actual results.
Request approval for implementation, and wait.

## Phase 3: Implement and Validate

- Implement only the approved scope. Keep unrelated cleanup separate.
- Run the repository's required formatting, tests, lint, and other validation.
- Report how each acceptance criterion is satisfied, any deviations from the
  approved design, residual risks, and failed or skipped checks.
- If implementation requires material contract or architectural changes, stop
  and obtain renewed design approval before continuing.
- Follow [Git Workflow Details](git-workflow-details.md#validation-before-committing)
  for separate approval before committing, pushing, or creating/updating a PR.

## Approval Semantics

- A general request to "fix this" starts the workflow; it does not bypass its stops.
- Approval applies only to the named phase, scope, and proposed behavior. Do not
  treat silence, tool permissions, or approval of an earlier phase as consent to
  proceed through later checkpoints.
- The user may explicitly combine design, test, and implementation gates for a
  named task. State the resulting scope before proceeding; do not infer a blanket
  waiver for future tasks.
- Material changes to contracts, architecture, or acceptance criteria invalidate
  the relevant approval. Minor implementation choices within the approved design
  do not require repeated approval.
- Design, test, or implementation approval never authorizes publication. Preserve
  the existing validation and publication approval requirements.

## Contracts and Design Artifacts

### Contract-First, Not Interface-First

Show proposed Go signatures and Godoc when they clarify a changed boundary.
Document concrete types or existing API behavior when no interface is warranted.
Keep proposed declarations in the design brief until implementation approval;
do not create speculative production abstractions during the design phase.

Do not require new interfaces for every change. Follow the existing
[Go review standards](pr-review-guidelines.md#go-code-review-standards): keep
interfaces small and consumer-owned, and avoid unnecessary abstractions.

### Architecture Decision Records

Use a minimal ADR for a significant, durable architectural decision. Include
context, the chosen solution, rejected alternatives, and consequences or
trade-offs. A local bug fix normally needs only the design brief, not an ADR.

Keep temporary proposals in the conversation unless a persistent draft is
requested or required by the repository. Store accepted, durable decisions in the
repository's established human-facing documentation location, following
[Documentation Guidelines](documentation-guidelines.md). Do not put project
architecture in this local workflow file or create a parallel source of truth.

### Diagrams

Include a Mermaid flowchart, state diagram, or sequence diagram in the design
brief for non-trivial state transitions, reconciliation, retries, concurrency,
or cross-component interactions. Do not require a diagram for every failure.

Distinguish observed current behavior from proposed behavior and mark unverified
transitions or assumptions. Diagrams explain a model; they do not prove a root
cause or replace supporting evidence. Each artifact must clarify a decision or
acceptance criterion rather than satisfy an artifact quota.
