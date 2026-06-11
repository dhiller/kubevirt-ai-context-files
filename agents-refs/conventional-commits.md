# Conventional Commits

All commit messages MUST follow the [Conventional Commits specification](https://www.conventionalcommits.org/en/v1.0.0/).

## Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Common Types

- **`fix`** - patches a bug (correlates with PATCH in SemVer)
- **`feat`** - introduces a new feature (correlates with MINOR in SemVer)
- **`docs`** - documentation only changes
- **`style`** - formatting, missing semicolons, etc; no code change
- **`refactor`** - code change that neither fixes a bug nor adds a feature
- **`perf`** - code change that improves performance
- **`test`** - adding or correcting tests
- **`build`** - changes to build system or dependencies
- **`ci`** - changes to CI configuration files and scripts
- **`chore`** - other changes that don't modify src or test files
- **`revert`** - reverts a previous commit

## Breaking Changes

- Append `!` after type/scope (e.g., `feat!:` or `feat(api)!:`)
- Include `BREAKING CHANGE:` in footer

## AI Agent Trailer

When an AI agent creates a commit, include an `Assisted-by:` trailer in the footer:

```
Assisted-by: <Agent Name> <agent-email>
```

## No Issue-Closing Keywords

Commit messages MUST NOT contain [GitHub issue-closing keywords](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/using-keywords-in-issues-and-pull-requests) followed by an issue reference (e.g., `#123`). Many repositories use Prow bots that flag these as invalid commit messages and block merging with a `do-not-merge/invalid-commit-message` label.

Forbidden keywords (case-insensitive): `close`, `closes`, `closed`, `fix`, `fixes`, `fixed`, `resolve`, `resolves`, `resolved`.

Use neutral phrasing instead: `Related to #4906`, `See #123 for context`.

## DCO Sign-off

Some repositories require DCO sign-off (`--signoff` / `-s` flag on `git commit`). Check the repository's own `AGENTS.md` for sign-off requirements.

## Examples

```
feat: add s390x cluster support to infrastructure diagram

docs: update AGENTS.md with conventional commits rule

fix(prow): correct duplicate subgraph IDs in Mermaid diagram

feat!: change API endpoint structure

BREAKING CHANGE: /api/v1 endpoints moved to /api/v2
```
