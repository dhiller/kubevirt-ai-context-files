# Feedback Rules

Behavioral rules derived from user corrections. Follow these to avoid repeating past mistakes.

## No Issue/PR References in Commit Messages

Never include GitHub issue or PR links or references inside commit messages (e.g. `#1234`, `Fixes #1234`, full URLs).

**Why:** Issue/PR references belong in PR descriptions, not commit messages.

**How to apply:** When composing `git commit` messages, omit any `Fixes #N`, `Closes #N`, issue URLs, or PR references. Place those in the PR body instead when creating or editing PRs.

## Trigger Tests in a Single Comment

When triggering multiple test lanes on a PR (e.g., `/test pull-kubevirt-e2e-...`), combine all `/test` lines into a single PR comment instead of posting one comment per job.

**Why:** Multiple separate comments create unnecessary noise on the PR.

**How to apply:** Join all `/test <job-name>` lines with newlines into one `gh pr comment --body` call.

## Review Command

When user requests to ask for a review, first check which code assist is active - usually it's one of 
* Gemini Code Assist or
* CodeRabbit

### Gemini Code Assist

Always use `/gemini review` as a PR comment to request a review from Gemini Code Assist. Do not use the GitHub API to add/remove requested reviewers.

**Why:** The `/gemini review` slash command is the supported way to trigger Gemini Code Assist reviews. Using the API reviewer request mechanism doesn't reliably trigger the review.

**How to apply:** When the user asks to request a Gemini review on a PR, post a comment with `/gemini review` via `gh pr comment`.

### CodeRabbit

To request a review use `@coderabbitai review` as a PR comment.

## Keep toolchain Directive When Bumping Go

When bumping the Go version in `go.mod`, do not remove or merge the `toolchain` directive into the `go` directive. Bump them independently: the `go` directive sets the minimum language version, the `toolchain` directive pins the exact compiler version.

**Why:** Removing `toolchain` causes Go to fall back to whatever toolchain is locally installed, losing reproducibility. The two directives serve different purposes and should be managed separately.

**How to apply:** When editing `go.mod` for a version bump, change only the directive the user asked to bump. If bumping the toolchain, leave the `go` line untouched and vice versa.

## No Compound or Piped Commands

Never combine commands using `&&`, `;`, `|`, or any other shell operator. Each tool call must contain exactly one simple command — no pipes, no chaining, no subshells.

**Why:** Claude Code permission patterns match on the full command string. `cd /foo && git log` doesn't match `Bash(git *)`, and `git log | head -20` doesn't match it either because the glob sees `git log | head -20` as the full string, not just the git part. Every compound/piped command triggers an approval prompt.

**How to apply:**
- **Instead of `cd dir && git log`** → use `git -C dir log` or run `cd` as a separate Bash call first
- **Instead of `git log | head -20`** → use `git log -n 20`
- **Instead of `git log | grep pattern`** → use `git log --grep='pattern'`
- **Instead of `git branch -a | grep foo`** → use `git branch -a --list '*foo*'`
- **Instead of `git diff | wc -l`** → use `git diff --stat`
- **Instead of `command | sort | uniq`** → use the command's own flags, or run the command first, then process the output in a second call
- In general: use the tool's built-in flags to filter/limit output. If no flag exists, run the command alone and process the result in a follow-up call.
