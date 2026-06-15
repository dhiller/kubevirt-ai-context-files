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

## Gemini Review Command

Always use `/gemini review` as a PR comment to request a review from Gemini Code Assist. Do not use the GitHub API to add/remove requested reviewers.

**Why:** The `/gemini review` slash command is the supported way to trigger Gemini Code Assist reviews. Using the API reviewer request mechanism doesn't reliably trigger the review.

**How to apply:** When the user asks to request a Gemini review on a PR, post a comment with `/gemini review` via `gh pr comment`.

## Keep toolchain Directive When Bumping Go

When bumping the Go version in `go.mod`, do not remove or merge the `toolchain` directive into the `go` directive. Bump them independently: the `go` directive sets the minimum language version, the `toolchain` directive pins the exact compiler version.

**Why:** Removing `toolchain` causes Go to fall back to whatever toolchain is locally installed, losing reproducibility. The two directives serve different purposes and should be managed separately.

**How to apply:** When editing `go.mod` for a version bump, change only the directive the user asked to bump. If bumping the toolchain, leave the `go` line untouched and vice versa.

## No Compound cd Commands

Never chain `cd` with other commands using `&&`, `;`, or pipes. Run `cd` separately, then the actual command in a follow-up call.

**Why:** Claude Code permission patterns match on the first token of the command string. `cd /foo && git log` starts with `cd`, so it doesn't match `Bash(git log *)` and triggers an approval prompt. This matters especially in the containerized harness where read operations are auto-approved.

**How to apply:** When you need to run a command in a different directory, either use the tool's own flag (e.g., `git -C /path log`) or run `cd` first as a separate Bash call, then the command in the next call.
