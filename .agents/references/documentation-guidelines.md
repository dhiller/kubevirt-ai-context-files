# Documentation Guidelines

## Documentation Placement and Design-First Execution

These guidelines govern where documentation belongs. For when to investigate,
propose a design, write tests, and implement behavioral changes, follow the
[Design-First Development Workflow](design-first-workflow.md).

Temporary design proposals may remain in the conversation unless a persistent
draft is requested or required by the repository. Put accepted, durable decisions
in established project documentation; keep the agent approval process in local
workflow guidance rather than presenting it as maintainer-approved project policy.

## Documentation-First Approach

- Before changing documentation or agent guidance, read the repository's existing
  documentation index, `README.md`, and relevant contributor guides. Update the
  canonical document rather than creating a parallel source of truth.
- Put reusable project knowledge (architecture, coding patterns, build/test
  commands, contribution and review policies, troubleshooting) in ordinary
  human-facing Markdown, not agent instructions or tool configuration.
- Reuse an existing documentation index, such as `docs/README.md`, or the
  repository README's documentation section. Add topic links there; create a new
  index only when no suitable entry point exists. Do not require a particular
  filename such as `docs_map.md`.
- Repository `AGENTS.md` files and vendor-specific adapters should point to that
  index or relevant documentation, not duplicate its contents. Keep inherited
  local workflow pointers separate from project documentation pointers.
- Tool configuration should contain operational settings and documentation
  references only. Do not upstream personal personas, local paths, remote names,
  tool choices, or contributor-specific workflow as project policy.
- When extracting local guidance, verify commands and paths against current
  source and authoritative policies. Link existing documentation instead of
  copying it, and distinguish local preferences from maintainer-approved rules.
- Preserve reachability when moving knowledge: check relative links and heading
  anchors from the local `AGENTS.md` through the documentation index to the
  transferred material. Verify ignored local entry points as well as PR files;
  do not stage local agent configuration unintentionally.
- Apply this as local working guidance. A proposal such as
  [community#444](https://github.com/kubevirt/community/pull/444) is not adopted
  project policy until merged; check its current status before claiming otherwise.

For Markdown formatting and validation, follow
[Markdown Guidelines](markdown-guidelines.md).
