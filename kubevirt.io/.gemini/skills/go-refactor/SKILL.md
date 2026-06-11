---
name: go-refactor
description: Refactor Go code in the kubevirt.io ecosystem following each repository's golangci-lint configuration, project conventions, and style guide.
argument-hint: <file-or-package-path>
allowed-tools: Bash(go *), Bash(golangci-lint *), Bash(gofmt *), Bash(goimports *), Bash(gofumpt *), Bash(git diff *), Bash(git status *), Read, Edit, Glob, Grep
---

Refactor Go code at `$ARGUMENTS` following the repository's linting rules, style guide, and code conventions.

If no file or package path is provided, ask the user what to refactor.

## Step 1: Identify the repository and its lint configuration

1. Determine which repository the target file belongs to (walk up to find `go.mod`)
2. Read the repository's `.golangci.yml` or `.golangci.yaml` configuration
3. Note the golangci-lint config version (v1 vs v2 format) — this affects key names
4. Check for comments referencing a parent config (many repos say "Taken from kubevirt/kubevirt")

## Step 2: Understand the active linter rules

Read the golangci-lint config and extract the enabled linters and their settings. The kubevirt.io ecosystem commonly uses these configurations:

### kubevirt/kubevirt (strictest — reference config for the ecosystem)
- **Linters**: bodyclose, copyloopvar, dogsled, dupl, errcheck, exhaustive, funlen, gochecknoinits, goconst, gocritic, gocyclo, goheader, goprintffuncname, gosec, govet (with shadow), ineffassign, lll, misspell, mnd, nakedret, noctx, nolintlint, rowserrcheck, staticcheck, unconvert, unused, whitespace
- **Formatters**: gofmt, gofumpt (extra-rules), goimports (local-prefixes: kubevirt.io/kubevirt)
- **Key thresholds**: dupl=100, funlen=100 lines/50 statements, gocyclo=15, lll=140, goconst min-len=2 min-occurrences=2
- **gocritic**: diagnostic + experimental + opinionated + performance + style tags enabled; hugeParam/rangeValCopy sizeThreshold=1024

### kubevirt/project-infra (lightweight)
- **Linters**: gocyclo, govet, ineffassign, staticcheck, unused

### kubevirt/containerized-data-importer
- **Linters**: dupword, durationcheck, errname, errorlint, gci, ginkgolinter, gofmt, goimports, gosec, govet, misspell, nakedret, revive, unconvert, usestdlibvars, whitespace
- **Import ordering** (gci): standard, github.com/onsi/, default, k8s.io, sigs.k8s.io, kubevirt.io

### kubevirt/ssp-operator (minimal)
- **Linters**: whitespace, misspell, ginkgolinter, errcheck, ineffassign, staticcheck, unused, errname

### Other repos
- Typically a subset of the kubevirt/kubevirt config — check comments in the config file for lineage

## Step 3: Run the linter on the target code

```bash
# From the repository root, run golangci-lint on the target
golangci-lint run ./path/to/package/...
```

If `golangci-lint` is not available locally, use the dockerized wrapper if present:
```bash
hack/dockerized "golangci-lint run ./path/to/package/..."
```

Or analyze the code manually against the rules.

## Step 4: Apply refactoring fixes

For each category of issue, apply the appropriate fix:

### Formatting (always fix)
- **gofmt/gofumpt**: Apply standard Go formatting with gofumpt extra rules
- **goimports**: Organize imports with local-prefix grouping per the config
- **whitespace**: Remove unnecessary blank lines in function bodies

### Complexity (refactor if above threshold)
- **funlen** (>100 lines or >50 statements): Extract logical sections into well-named helper functions
- **gocyclo** (>15): Reduce cyclomatic complexity — extract conditions, use early returns, simplify branching
- **dupl** (>100 tokens): Extract duplicated code into shared functions

### Correctness (always fix)
- **errcheck**: Handle all returned errors — no silent discards
- **govet/shadow**: Eliminate variable shadowing (use different names or `:=` carefully)
- **bodyclose**: Ensure HTTP response bodies are closed
- **noctx**: Pass context.Context to HTTP requests
- **rowserrcheck**: Check sql.Rows.Err() after iteration
- **staticcheck**: Fix all SA/S/ST/QF issues (except excluded ones per config)
- **unused**: Remove unused code

### Style (fix to match project conventions)
- **goconst**: Extract repeated string/number literals (min 2 chars, 2 occurrences) into constants
- **mnd** (magic number detector): Replace magic numbers with named constants (except in test helpers like Eventually/ExpectWithOffset)
- **nakedret**: Add explicit return values to named-return functions
- **lll** (>140 chars): Break long lines — prefer breaking at logical points (after commas, before operators)
- **misspell**: Fix spelling errors (US English locale)
- **unconvert**: Remove unnecessary type conversions
- **gocritic**: Apply opinionated style fixes per the enabled tags
- **errname**: Error type names should have `Error` suffix, error variables should have `Err` prefix
- **gosec**: Address security issues (but G601 is commonly excluded for Go >= 1.22)

### Test code conventions
- **ginkgolinter**: Follow Ginkgo best practices, no focused specs (forbid-focus-container: true)
- Dot imports are allowed for `github.com/onsi/ginkgo/v2` and `github.com/onsi/gomega`

## Step 5: Apply the style guide

Beyond lint violations, apply these project conventions when refactoring:

### Naming
- Use idiomatic Go naming: MixedCaps/mixedCaps, not underscores
- Acronyms are all-caps when exported (`VMI`, `API`, `HTTP`), lowercase when unexported (`vmi`, `api`)
- Error variables: `var ErrSomething = errors.New("...")` — `Err` prefix, `Error` suffix on types
- Interface names: single-method interfaces use `-er` suffix (`Reader`, `Reconciler`)
- Package names: short, lowercase, no underscores — avoid generic names like `util` or `common`

### Function design
- Prefer early returns to reduce nesting — guard clauses at the top
- Functions should do one thing; if a function needs a comment explaining a section, extract that section
- Accept interfaces, return concrete types
- Context should be the first parameter: `func Foo(ctx context.Context, ...)`
- Options pattern for functions with many parameters: use functional options or a config struct

### Error handling
- Wrap errors with context: `fmt.Errorf("doing X: %w", err)`
- Do not use `errors.New` for errors that should be matched — use sentinel errors or typed errors
- Never ignore errors silently; if intentional, assign to `_` with a comment explaining why
- Use `%w` (not `%v`) to preserve error chains for `errors.Is`/`errors.As`

### Import ordering
The canonical import group order (enforced by gci/goimports):
1. Standard library
2. Ginkgo/Gomega (`github.com/onsi/`)
3. Third-party packages
4. Kubernetes packages (`k8s.io/`, `sigs.k8s.io/`)
5. KubeVirt packages (`kubevirt.io/`)

Each group separated by a blank line.

### Struct and type design
- Prefer composition over inheritance (embedding)
- Group related fields; separate groups with blank lines and comments
- Avoid large parameter structs passed by value — gocritic's `hugeParam` threshold is 1024 bytes
- Use pointer receivers for methods that mutate state; value receivers for pure computations

### Constants and magic values
- Extract magic numbers into named constants with descriptive names
- Group related constants in `const` blocks
- Use `iota` for enumerated constants when appropriate
- String constants used more than once should be extracted (goconst: min-len=2, min-occurrences=2)

### Comments
- Exported symbols must have godoc comments starting with the symbol name
- Package comments go in a `doc.go` file for non-trivial packages
- Use `// TODO(username):` for deferred work
- Do not add comments that merely restate the code — only comment "why", not "what"

### Kubernetes controller patterns
- Controllers follow watch-reconcile pattern
- Use `client-go` informers and listers for cached access
- Handle `NotFound` errors gracefully in reconcile loops (resource may be deleted)
- Use `controller-runtime`'s `Result{Requeue: true}` for retry, not busy loops

### Test patterns (Ginkgo/Gomega)
- Use `Describe`/`Context`/`It` hierarchy — `Describe` for the unit, `Context` for conditions, `It` for assertions
- No focused specs (`FDescribe`, `FContext`, `FIt`) — CI will reject them
- Use `DeferCleanup` over `AfterEach` for cleanup that pairs with setup
- Prefer `Expect(err).ToNot(HaveOccurred())` over `Expect(err).To(BeNil())`
- Use `Eventually`/`Consistently` for async assertions with explicit timeout and polling interval
- Test descriptions should read as sentences: `It("should create a VM when ...")`

## Step 6: Verify the refactoring

1. Run the linter again to confirm all issues are resolved:
   ```bash
   golangci-lint run ./path/to/package/...
   ```
2. Run `go vet ./path/to/package/...` as a baseline check
3. Run unit tests for the affected packages:
   ```bash
   go test ./path/to/package/...
   ```
4. If the repository uses Bazel, note that `make generate` may need to be run for BUILD file updates
5. Run `git diff` and review the changes — verify no behavioral changes were introduced

## Guidelines

- **Minimal changes**: Only fix actual violations and apply style guide conventions — do not refactor working code that already conforms
- **Preserve behavior**: Refactoring must not change program semantics
- **Respect exclusions**: Honor path exclusions and rule exclusions in the config (e.g. vendor/, tests/network/ files in kubevirt)
- **nolint directives**: If a lint issue cannot be fixed without changing behavior, add a `//nolint:lintername // reason` comment — but only as a last resort
- **Import ordering**: Follow the repository's gci/goimports local-prefix convention
- **Test helpers**: Magic numbers in Ginkgo matchers (Eventually, ExpectWithOffset, etc.) are exempt from mnd
- **Scope discipline**: When refactoring a function, fix issues within that function and its immediate helpers — do not cascade into unrelated code
- **Commit granularity**: Suggest separate commits for formatting-only changes vs. behavioral refactors
