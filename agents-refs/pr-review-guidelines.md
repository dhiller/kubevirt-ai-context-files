# PR Review Guidelines

## General

When approving PRs, keep comments sparse and concise. Thank the author with an emoji medal. Focus on brief acknowledgment and essential notes only.

## Submit Reviews as Draft

Always submit PR reviews as drafts first. This lets you revisit and refine comments before the author is notified. Only publish the review once all files have been reviewed and comments are finalized.

## Distinguish Nits from Errors

Every review comment must clearly signal its severity:

- **Nit:** — style, naming, or minor improvement suggestions that should not block merging. Prefix with `nit:` or `nitpick:`.
- **Error / Bug:** — correctness issues, logic errors, security concerns, or contract violations that must be addressed before merging. State the problem and why it matters.

When a review contains only nits, approve the PR rather than requesting changes.

## Go Code Review Standards

When reviewing Go code, hold it to idiomatic Go standards:

- **Naming** — follow Go conventions: `MixedCaps`/`mixedCaps` (no underscores), short receiver names, acronyms fully capitalized (`HTTPClient`, not `HttpClient`). Package names are lowercase single words, not `snake_case` or `camelCase`.
- **Error handling** — errors must be checked, not discarded. Prefer `fmt.Errorf("context: %w", err)` for wrapping. No `panic` in library code.
- **Interfaces** — accept interfaces, return concrete types. Keep interfaces small (1-3 methods). Define interfaces at the consumer, not the implementor.
- **Package design** — no circular dependencies, no `package utils` or `package common` grab-bags. Exported symbols should have doc comments.
- **Simplicity** — prefer standard library over external dependencies. Flag unnecessary abstractions, premature generics, or over-engineered patterns.

## Bash Script Review Standards

When reviewing shell scripts, hold them to shellcheck and bash best practices:

- **shellcheck** — all scripts should pass `shellcheck`. Cite the specific rule (e.g. SC2086) when flagging violations.
- **Strict mode** — scripts should set `set -euo pipefail` near the top. Flag missing error handling.
- **Quoting** — all variable expansions must be double-quoted (`"$var"`, `"${array[@]}"`) unless word splitting is intentionally required.
- **Command substitution** — use `$(cmd)` not backticks. Quote the result: `local out="$(cmd)"`.
- **Conditionals** — use `[[ ]]` over `[ ]` for tests. Use `(( ))` for arithmetic.
- **Portability** — if the shebang is `#!/bin/sh`, the script must not use bash-isms. If bash features are needed, use `#!/usr/bin/env bash` or `#!/bin/bash`.
- **Naming** — local variables in `snake_case`, environment/exported variables in `UPPER_CASE`. Use `local` for function-scoped variables.
- **Temporary files** — use `mktemp` and clean up with a `trap` on `EXIT`. No hardcoded `/tmp/foo` paths.
- **Subshells and pipes** — avoid unnecessary subshells. Prefer `while read` with process substitution over piping into a loop (variables set inside a piped loop are lost).
