---
name: pending-reviews
description: List open pull requests pending my review, grouped by repository and sorted by most recently updated.
when_to_use: Use when the user asks to see pending reviews, show PRs awaiting review, list review requests, check what PRs need review, or any variation of "show my pending reviews".
allowed-tools: Bash(gh *)
---

List all open GitHub pull requests where the current user is requested as a reviewer, excluding drafts and PRs that are not ready for review.

## Steps

1. Run: `gh search prs --review-requested=@me --state=open --draft=false --limit 50 --json repository,title,number,url,updatedAt,author,labels`
2. Parse the JSON results.
3. Filter out PRs that have a label matching any of: `do-not-merge/work-in-progress`, `needs-rebase`, `wip` (case-insensitive).
4. Filter out PRs that have both the `lgtm` and `approved` labels AND do not have any label starting with `do-not-merge` (these are ready to merge and don't need review).
5. Group remaining PRs by repository (`nameWithOwner`).
6. Within each repository group, sort PRs by `updatedAt` descending (most recent first).
7. Order the repository groups by the most recent PR in each group (repos with newer activity first).

## Output format

For each repository, render a markdown table:

### {repository nameWithOwner}

| Updated | Title | Author | URL |
|---------|-------|--------|-----|
| {date} | {title} | {author login} | {url} |

- Format `updatedAt` as short date (e.g. "Apr 17").
- End with a total count line: **Total: N open PRs** across M repositories.
