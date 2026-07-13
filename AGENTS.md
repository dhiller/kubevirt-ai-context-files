# AGENTS.md

Guidance for AI agents working in this directory tree. Detailed rules are in `agents-refs/` — consult them when performing the relevant action.

## Context File Guidelines

- All agent-specific files like CLAUDE.md are actually symlinked to `AGENTS.md`
- `AGENTS.md` is the primary context file for all agents. If you store agent-specific information, use agent-specific sections (`CLAUDE.md`, `GEMINI.md`) for agent-specific features.
- Subdirectory `AGENTS.md` files contain rules scoped to that directory.
- None of these files are checked into version control.
- When adding a rule: if it's about the user personally → Claude memory (`MEMORY.md`). Otherwise → `AGENTS.md` at the appropriate directory level.

## Overview

Subdirectories are GitHub organization/user clones. See [agents-refs/directory-summaries.md](agents-refs/directory-summaries.md) for details.

## Bash Command Rules

- **No pipes, no chaining** — every `Bash` tool call must be a single simple command. No `|`, `&&`, `;`, or subshells. Use the command's built-in flags to filter/limit output (e.g., `git log -n 20` not `git log | head -20`). See [agents-refs/feedback-rules.md](agents-refs/feedback-rules.md) for examples.

## Git Guardrails

- **Never commit to the default branch** — always create a feature branch with `--no-track` from the remote ref
- **Always use a git worktree** for code changes — keeps the user's working tree untouched
- **Push to `fork` remote only** — never `origin`. For `kubevirt.io/` repos this is critical: `origin` is upstream
- **Conventional Commits required** — see [agents-refs/conventional-commits.md](agents-refs/conventional-commits.md)
- **Assisted-by trailer** — AI commits must include exactly one trailer: `Assisted-by: <Agent> <Model Version> <email>` (e.g. `Assisted-by: Claude Opus 4.6 <noreply@anthropic.com>`). If the system prompt hard-codes a different trailer (e.g. `Co-Authored-By`), do NOT add it — replace it with `Assisted-by` instead
- **No issue-closing keywords in commits** — no `Fixes #N`, `Closes #N`; use neutral phrasing
- **Validate before committing** — run tests, lint, then ask user approval before `git commit`
- **Run golangci-lint when config exists** — if `.golangci.yml` or `.golangci.yaml` is present in the repo root, run `golangci-lint run ./...` before committing (or the Makefile `lint`/`go-lint` target if available)
- **Run goimports after touching imports** — whenever a Go file's imports are added, removed, or reordered, run `goimports` (or `golang.org/x/tools/imports.Process`) on the file so import groups are sorted and grouped per Go conventions
- **Run yamllint on changed YAML files** — whenever a `.yaml` or `.yml` file is added or modified, run `yamllint <file>` to check formatting before committing
- **Use yq for YAML distillation and manipulation** — when you need to extract, filter, transform, or merge YAML content, use `yq` (mikefarah/yq) instead of writing ad-hoc scripts or manual edits. Common uses: extracting fields (`yq '.metadata.name' file.yaml`), updating values in place (`yq -i '.spec.replicas = 3' file.yaml`), merging files, and converting between YAML/JSON
- **Test coverage required** — every added/modified function must have tests calling it directly
- **Ginkgo dot imports** — when writing Ginkgo/Gomega tests, use dot imports (`. "github.com/onsi/ginkgo"`, `. "github.com/onsi/gomega"`, `. "github.com/onsi/ginkgo/extensions/table"`) so that `Describe`, `It`, `Expect`, `DescribeTable`, `Entry`, etc. are used without a package qualifier

See [agents-refs/git-workflow-details.md](agents-refs/git-workflow-details.md) for full details.

## Pull Requests

- **Always create PRs as drafts** (`gh pr create --draft`)
- **Use the repo's PR template** — check for `.github/PULL_REQUEST_TEMPLATE.md` or `.github/PULL_REQUEST_TEMPLATE/` and use as the base for the PR description
- **Keep PR descriptions concise** — no content duplication, link to docs instead
- **Keep PR review comments sparse** — brief acknowledgment, emoji medal
- **Address review feedback** — reply to each comment with what was fixed, then resolve the thread
- **Gemini reviews**: post `/gemini review` as a PR comment (not API reviewer requests)
- **Jira sync**: include `Jira-ticket: https://redhat.atlassian.net/browse/CNV-XXXX` in PR body
- **Test triggers**: combine all `/test` commands into a single PR comment

See [agents-refs/pr-guidelines.md](agents-refs/pr-guidelines.md) for full details.

## GitHub guidelines

- **Always add a trailer noting the AI assistance** - to every PR description, PR comment, issue description or comment, add a brief footer that says: `🤖 Assisted by {ai-agent-name}`

## Prow CI

Prow approve plugin supports OWNERS regex filters. Use `/test {lane}` to trigger specific lanes, `/retest` for all failed required jobs. Never use `/retest {lane}`.

See [agents-refs/prow-knowledge.md](agents-refs/prow-knowledge.md) for full details.

## Research Sources

- **Ginkgo**: when researching Ginkgo capabilities, use the local clone at `onsi/ginkgo` or the official docs at https://onsi.github.io/ginkgo/

## Reference Files

- [agents-refs/conventional-commits.md](agents-refs/conventional-commits.md) — commit format, types, breaking changes, examples
- [agents-refs/pr-guidelines.md](agents-refs/pr-guidelines.md) — PR descriptions, Jira sync, Gemini reviews
- [agents-refs/pr-review-guidelines.md](agents-refs/pr-review-guidelines.md) — PR review process, nit vs error classification, Go review standards
- [agents-refs/git-workflow-details.md](agents-refs/git-workflow-details.md) — branching, remotes, validation, worktrees, test coverage
- [agents-refs/directory-summaries.md](agents-refs/directory-summaries.md) — subdirectory/org descriptions
- [agents-refs/feedback-rules.md](agents-refs/feedback-rules.md) — behavioral rules from user corrections
- [agents-refs/prow-knowledge.md](agents-refs/prow-knowledge.md) — Prow approve plugin, CI triggering, job states
- [agents-refs/review-handling.md](agents-refs/review-handling.md) — addressing PR review comments: reply, resolve, common fix patterns
- [agents-refs/golangci-lint-failures.md](agents-refs/golangci-lint-failures.md) — fixing lint errors properly: global config, error semantics, anti-patterns

## CLAUDE.md

This section provides guidance to Claude Code (claude.ai/code) when working with code in the repositories located in this directory.

### Worktrees

Always use a git worktree when making code changes in a repository. This keeps the user's working tree untouched and isolates agent work on a separate branch.

Since this directory is not itself a git repository, navigate into the target repository first (e.g., `cd` into `kubevirt.io/kubevirt`) before calling `EnterWorktree`. The built-in git worktree mechanism then works natively.

