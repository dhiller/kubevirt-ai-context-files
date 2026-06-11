# Handling PR Review Comments

How to address review comments (from Gemini Code Assist, human reviewers, or other bots) on a pull request.

## Workflow

1. **Fetch all review comments** — use `gh api repos/{owner}/{repo}/pulls/{number}/comments` to get every comment with its ID, path, line, and body.
2. **Implement the fix** for each comment in the codebase.
3. **Validate** — run lint, tests, and goimports before committing.
4. **Commit and push** — a single commit addressing all review feedback is preferred over one commit per comment.
5. **Reply to each comment** — post a reply on the comment thread explaining what was done. Include the commit SHA for traceability.
6. **Resolve the thread** — after replying, resolve the thread via the GraphQL API.
7. **Request a new review** — once all threads are addressed and resolved, ask the reviewer for another pass. For Gemini Code Assist, post `/gemini review` as a PR comment. For human reviewers, use `gh pr edit --add-reviewer {user}` or ask the user whether to re-request review.

## Fetching Comments

```bash
# REST: list all review comments with IDs
gh api repos/{owner}/{repo}/pulls/{number}/comments --paginate

# Useful jq filter for overview
| jq '[.[] | {id, path, line, body: (.body | split("\n")[0:3] | join("\n")), user: .user.login}]'
```

## Replying to Comments

Reply to a specific comment thread using its comment ID:

```bash
gh api repos/{owner}/{repo}/pulls/{number}/comments/{comment_id}/replies \
  -f body="Fixed in {commit_sha} — {brief description of what changed}.

🤖 Assisted by {agent-name}"
```

Keep replies concise: state what was fixed and how. Include a code snippet only if the fix pattern is non-obvious.

## Resolving Threads

The REST API does not support resolving review threads. Use the GraphQL API:

### Step 1: Find unresolved thread IDs

```bash
gh api graphql -f query='
{
  repository(owner: "{owner}", name: "{repo}") {
    pullRequest(number: {number}) {
      reviewThreads(first: 50) {
        nodes {
          id
          isResolved
          comments(first: 1) {
            nodes { path body }
          }
        }
      }
    }
  }
}' | jq '[.data.repository.pullRequest.reviewThreads.nodes[] | select(.isResolved == false)]'
```

### Step 2: Resolve each thread

```bash
gh api graphql -f query='
mutation {
  resolveReviewThread(input: {threadId: "{thread_node_id}"}) {
    thread { isResolved }
  }
}'
```

## Reply Content Guidelines

- **Start with the commit SHA** — e.g., "Fixed in abc1234"
- **Describe the fix briefly** — what changed and why it addresses the concern
- **Include a code snippet** only when the fix pattern is non-obvious or the reviewer suggested a specific approach worth confirming
- **End with the AI trailer** — `🤖 Assisted by {agent-name}`
- **Do not argue with valid feedback** — if the reviewer is right, fix it and say so
- **Only push back with evidence** — if a suggestion would introduce a regression or is based on a misunderstanding, explain why with references to the code

## Severity Handling

Address comments in priority order:

1. **HIGH / Bug / Error** — must be fixed before the PR can merge. These indicate correctness issues, data loss risks, or security concerns.
2. **MEDIUM** — should be fixed. These are anti-patterns, inconsistencies, or maintainability concerns.
3. **LOW / Nit** — fix if straightforward, otherwise acknowledge and defer. Do not block on nits.

## Common Patterns

### "Use global config instead of inline suppression"
Move repeated inline annotations (e.g., `//nolint:errcheck` on `fmt.Fprint*`) to the linter config file. Remove all inline occurrences. Verify with `golangci-lint run ./...`.

### "Check this error on the success path"
When a `Close()` or similar call performs real work (e.g., GCS uploads, flushing buffers), replace `_ = x.Close()` with explicit error checking on the success path. Deferred cleanup on error paths can still ignore the error.

### "Inconsistent error handling"
When a function mixes checked and unchecked error handling for the same operation, make it consistent. Prefer a helper closure when there are many calls:

```go
var writeErr error
write := func(s string) {
    if writeErr == nil {
        _, writeErr = w.Write([]byte(s))
    }
}
// ... all calls use write() ...
return writeErr
```

### "Anti-pattern: operating on an open file by name"
When the file is already open (e.g., from `os.CreateTemp`), use the existing file handle directly (`temp.Write(data)`) instead of reopening by path (`os.WriteFile(temp.Name(), data, perm)`).
