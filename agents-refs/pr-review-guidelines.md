# PR Review Guidelines

## General

When approving PRs, keep comments sparse and concise. Thank the author with an emoji medal. Focus on brief
acknowledgment and essential notes only.

## Submit Reviews as Draft

Always submit PR reviews as drafts first. This lets you revisit and refine comments before the author is notified. Only
publish the review once all files have been reviewed and comments are finalized.

## Distinguish Nits from Errors

Every review comment must clearly signal its severity:

- **Nit:** — style, naming, or minor improvement suggestions that should not block merging. Prefix with `nit:` or
  `nitpick:`.
- **Error / Bug:** — correctness issues, logic errors, security concerns, or contract violations that must be addressed
  before merging. State the problem and why it matters.

When a review contains only nits, approve the PR rather than requesting changes.

## Go Code Review Standards

When reviewing Go code, hold it to idiomatic Go standards:

- **Naming** — follow Go conventions: `MixedCaps`/`mixedCaps` (no underscores), short receiver names, acronyms fully
  capitalized (`HTTPClient`, not `HttpClient`). Package names are lowercase single words, not `snake_case` or
  `camelCase`.
- **Error handling** — errors must be checked, not discarded. Prefer `fmt.Errorf("context: %w", err)` for wrapping. No
  `panic` in library code.
- **Interfaces** — accept interfaces, return concrete types. Keep interfaces small (1-3 methods). Define interfaces at
  the consumer, not the implementor.
- **Package design** — no circular dependencies, no `package utils` or `package common` grab-bags. Exported symbols
  should have doc comments.
- **Simplicity** — prefer standard library over external dependencies. Flag unnecessary abstractions, premature
  generics, or over-engineered patterns.

### Go vendor/ and go.mod/go.sum changes

#### go.mod / go.sum

- Verify new dependencies are intentional and justified — not pulled in accidentally via a transitive upgrade
- Check for unexpected major version bumps that could introduce breaking changes
- Watch for replace directives — they can mask upstream issues or pin to forks indefinitely
- Ensure the Go version directive (go 1.XX) matches what CI and the team actually use
- Flag removals of dependencies that are still imported in the codebase

#### vendor/

- vendor/ diffs should be machine-generated only — never hand-edited. The golden rule: go mod tidy && go mod vendor
  should reproduce the exact diff
- Review should focus on go.mod changes; the vendor/ tree is a consequence, not the source of truth
- Flag any non-generated file appearing in vendor/ (test files, docs, binaries)
- Watch for vendor/modules.txt being out of sync with go.mod

#### General

- Dependency updates should ideally be in their own commit/PR, separate from functional changes, so the actual code
  diff is reviewable 
- Security: check if any added/upgraded dependency has known CVEs
- License: new dependencies should have compatible licenses
- Size: flag large new dependencies for a simple task — prefer stdlib where possible
  
For the .coderabbit.yaml context, the repo already has "!vendor/**" in path_filters which excludes
vendor from review entirely. If you wanted CodeRabbit to enforce rules on go.mod/go.sum, you could
add a path entry for "**/go.mod" with instructions like "verify replace directives are justified,
flag unexpected major version bumps, ensure no unnecessary new dependencies."

## Bash Script Review Standards

When reviewing shell scripts, hold them to shellcheck and bash best practices:

- **shellcheck** — all scripts should pass `shellcheck`. Cite the specific rule (e.g. SC2086) when flagging violations.
- **Strict mode** — scripts should set `set -euo pipefail` near the top. Flag missing error handling.
- **Quoting** — all variable expansions must be double-quoted (`"$var"`, `"${array[@]}"`) unless word splitting is
  intentionally required.
- **Command substitution** — use `$(cmd)` not backticks. Quote the result: `local out="$(cmd)"`.
- **Conditionals** — use `[[ ]]` over `[ ]` for tests. Use `(( ))` for arithmetic.
- **Portability** — if the shebang is `#!/bin/sh`, the script must not use bash-isms. If bash features are needed, use
  `#!/usr/bin/env bash` or `#!/bin/bash`.
- **Naming** — local variables in `snake_case`, environment/exported variables in `UPPER_CASE`. Use `local` for
  function-scoped variables.
- **Temporary files** — use `mktemp` and clean up with a `trap` on `EXIT`. No hardcoded `/tmp/foo` paths.
- **Subshells and pipes** — avoid unnecessary subshells. Prefer `while read` with process substitution over piping into
  a loop (variables set inside a piped loop are lost).
