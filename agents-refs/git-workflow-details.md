# Git Workflow Details

## Validation Before Committing

After any changes in a repository, ALWAYS ask the user for validation before committing or updating a PR.

This includes: code refactoring, library replacements, algorithm changes, test modifications.

Steps:
1. Complete the refactoring
2. Run any repository-specific validation commands documented in the repo's `AGENTS.md` (e.g., formatters, `make validate`)
3. Run tests to ensure everything works
4. Lint the modifications:
   - If the repo's Makefile has a `lint` (or `go-lint`) target, run it
   - Otherwise, if a `.golangci.yml` / `.golangci.yaml` config exists, run `golangci-lint run ./...`
5. **ASK the user to validate the changes**
6. Only after user approval: commit and push

## Branching

NEVER commit directly to the default branch (usually `main` or `master`). Always create a feature branch.

When creating a branch from a remote ref (e.g. `origin/main`), always use `--no-track` to avoid setting an upstream tracking branch. A tracked upstream means `git push` without arguments would push to `origin`, which is the upstream repository.

```bash
git checkout --no-track -b <descriptive-branch-name> origin/main
```

## Worktrees

Always use a git worktree when making code changes. This keeps the user's working tree untouched and isolates agent work on a separate branch.

## Git Remote Configuration

When pushing before creating a PR:
- Always use the remote named `fork`
- Example: `git push fork <branch-name>` instead of `git push origin <branch-name>`

For any repository under `kubevirt.io/`: NEVER push to the `origin` remote. The `origin` remote points to the upstream `kubevirt/` GitHub organization. Pushing there creates stray branches in the upstream repo. Always use `fork`. This applies to all operations: regular pushes, force pushes, and branch deletions.

## Handling golangci-lint Failures

When lint fails, fix the root cause — don't just silence it. Global config over inline `//nolint`, understand errors before suppressing, consistent error handling, no new anti-patterns. See [golangci-lint-failures.md](golangci-lint-failures.md) for details.

## Ginkgo Dot Imports

When writing or modifying Ginkgo/Gomega tests, always use dot imports for the test framework packages:

```go
import (
	. "github.com/onsi/ginkgo"
	. "github.com/onsi/ginkgo/extensions/table"
	. "github.com/onsi/gomega"
)
```

This allows using `Describe`, `Context`, `It`, `Expect`, `DescribeTable`, `Entry`, etc. without a package qualifier, which is the established convention across the KubeVirt ecosystem.

## Test Coverage for Touched Code

When modifying or adding code, ensure adequate test coverage:
- Every added or modified function/method must be covered by tests that call it directly
- Do NOT replicate production logic inside tests — call the actual function under test
- If a function has external dependencies, refactor to make the dependency injectable rather than copying logic into the test
- When reviewing existing code that lacks coverage, add tests for the functions being touched
