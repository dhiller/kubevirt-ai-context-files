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
