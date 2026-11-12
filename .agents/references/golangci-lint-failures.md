# Handling golangci-lint Failures

When golangci-lint reports errors, fix them properly — don't just silence the linter. Common pitfalls (based on [kubevirt/project-infra#5132](https://github.com/kubevirt/project-infra/pull/5132)):

## Prefer global config over inline `//nolint` comments

If the same suppression (e.g., `//nolint:errcheck` on `fmt.Fprint*`) would appear in many places, configure the exclusion globally in `.golangci.yaml` instead:

```yaml
linters-settings:
  errcheck:
    exclude-functions:
      - fmt.Fprint
      - fmt.Fprintf
      - fmt.Fprintln
```

## Don't blindly suppress errors — understand what they mean

Some `Close()` calls perform real work. GCS writers (`*storage.Writer`) upload data during `Close()`, so ignoring that error with `_ = writer.Close()` silently drops upload failures. Before suppressing an error, verify the call has no meaningful side effects.

When `Close()` is meaningful: explicitly check its error on the success path and only use a deferred no-check close for cleanup on error paths.

## Handle errors consistently within a function

Don't mix checked and unchecked error handling in the same function (e.g., checking `WriteString` errors on the first few calls but using `_, _ =` on the rest). Either check all of them or use a helper that accumulates errors.

## Don't introduce new anti-patterns while fixing old ones

Example: replacing `os.WriteFile` on an already-open `*os.File` from `os.CreateTemp` — this opens a second file descriptor to the same file. Instead, write to the existing open file handle directly with `temp.Write(data)`.

## Summary: preferred approach to lint fixes

1. **Read the lint error carefully** — understand the root cause, not just the symptom
2. **Fix the actual problem** — proper error handling, correct API usage
3. **Use global config for widespread suppressions** — keep inline `//nolint` rare
4. **Only suppress with `//nolint` when the error is genuinely safe to ignore** — and add a brief comment explaining why
