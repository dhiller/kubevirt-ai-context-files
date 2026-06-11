# Pull Request Guidelines

## Draft PRs

Always create PRs as drafts: `gh pr create --draft`. The author marks them ready for review when appropriate.

## PR Descriptions

Be as concise as possible. Do NOT duplicate content that exists in the PR's files.

### Use the repository's PR template

When creating a PR for any repository, check for PR templates in this order:

1. **Single template:** If `.github/PULL_REQUEST_TEMPLATE.md` exists, use it as the base for the PR description.
2. **Multiple templates:** If `.github/PULL_REQUEST_TEMPLATE/` exists and contains `.md` files, list them and ask the user which template to use.
3. If neither exists, follow the default structure described below.

Fill in the relevant sections of the chosen template and remove or leave empty any sections that don't apply. Checklists should be included as-is for reviewers.

### Include Only

- Brief summary of what the PR does
- Key features or changes (high-level bullets)
- Quick start commands (minimal examples)
- Benefits/outcomes
- Links to detailed documentation
- Related PRs

### Avoid Including

- Detailed usage examples (link to README instead)
- Full command-line option lists (link to docs instead)
- Long code snippets (link to files instead)
- Step-by-step guides (link to documentation instead)
- File listings with line counts (reviewers can see the files)

### Example Structure

```markdown
## Summary
[Brief description and problem solved]

## Features
- Feature 1
- Feature 2

## Quick Start
[Minimal usage example]

See [README.md](path/to/README.md) for detailed usage and examples.

## Benefits
- Benefit 1
- Benefit 2

## Related
- #123 - Related PR
```

## PR Reviews

See [pr-review-guidelines.md](pr-review-guidelines.md) for full review guidelines.

## Jira-to-PR Synchronization

When a PR relates to a Jira issue, include in the PR body:

```
Jira-ticket: https://redhat.atlassian.net/browse/CNV-XXXX
```

## Addressing Review Comments

When working on a PR with existing review comments (Gemini Code Assist, human reviewers, or other bots), check for unaddressed comments. Offer to address them on the user's behalf.

See [review-handling.md](review-handling.md) for the full workflow: fetching comments, replying, resolving threads, and common fix patterns.

## Requesting Gemini Reviews

Use `/gemini review` as a PR comment to request a review from Gemini Code Assist. Do not use the GitHub API reviewer request mechanism.

**Why:** The `/gemini review` slash command is the supported trigger. API reviewer requests don't reliably start the review.
