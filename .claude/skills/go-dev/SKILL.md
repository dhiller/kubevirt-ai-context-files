---
name: go-dev
description: Standard Go development workflow — format, vet, build, and test. Works across any Go repo under kubevirt.io/ (and beyond). Optionally scope to a single component.
argument-hint: [component]
allowed-tools: Bash(go *), Bash(make *), Bash(gofmt *), Bash(ls *), Bash(grep *), Glob, Read
---

Run the standard Go development cycle for the current repository.

If $ARGUMENTS is given, scope operations to that component.
Otherwise, run against the full project.

## Detecting the repository

1. Determine the repo root by finding the nearest parent directory containing `go.mod` (starting from the current working directory).
2. `cd` into that directory for all subsequent commands.
3. If no `go.mod` is found, stop and tell the user this doesn't appear to be a Go project.

## Discovering Go packages

Do NOT hardcode package directories. Instead:

1. Find all directories containing `.go` files: look for top-level directories like `cmd/`, `pkg/`, `internal/`, `robots/`, `external-plugins/`, `releng/`, `tools/`, `controllers/`, `webhooks/`, `api/`, `staging/`, `tests/` — but verify they exist before using them.
2. For the **full project**, collect all such directories and use `./...` or list them explicitly.
3. For a **scoped component**, search for a directory matching the component name under the discovered Go directories (e.g. `pkg/<component>/`, `cmd/<component>/`, `robots/<component>/`). Include all matching paths.

## Full project steps

1. **Format**: `gofmt -l -w` on all discovered Go source directories.
   — If files are listed, they were reformatted; note which ones.
2. **Vet**: `go vet ./...`
3. **Build**: If `Makefile` exists and has a `build` target, use `make build`. Otherwise `go build ./...`.
4. **Test**: If `Makefile` exists and has a `test` target, use `make test`. Otherwise `go test ./...`.
5. **Lint** (optional, slower): If `Makefile` has a `lint` target, use `make lint`.

## Scoped steps (single component)

1. **Format**: `gofmt -l -w` on matched component directories.
2. **Build**: `go build <matched paths>/...`
3. **Test**: `go test <matched paths>/...`

## Rules

- Stop at the first failing step and report the exact error output.
- When checking for Makefile targets, use `grep -E '^targetname[^a-zA-Z_-]*:' Makefile`.
