---
name: copy-lanes
description: Copy kubevirt SIG test lanes (periodic + presubmit job definitions) for a new kubevirtci provider (k8s version) in project-infra, validate, spread schedules, and create a draft PR.
argument-hint: [k8s-version, e.g. 1.36]
allowed-tools: Bash(go *), Bash(make *), Bash(ls *), Bash(git *), Bash(gh *), Bash(podman *), Bash(rm *), Glob, Grep, Read
---

Copy kubevirt e2e test lanes for a new kubevirtci provider version, validate, commit, spread periodic schedules, and open a draft PR.

A "lane" is a set of Prow job definitions (presubmit + periodic) for a specific k8s version and SIG (sig-network, sig-storage, sig-compute, sig-operator). This skill runs the `kubevirt copy jobs` robot from `project-infra` to create new lane definitions based on the previous k8s version.

## Approval checkpoints

Follow the shared
[Design-First Development Workflow](../../../agents-refs/design-first-workflow.md)
before changing CI behavior. Present the intended lane contracts, schedule impact,
dry-run evidence, and validation plan for design approval. Then prepare the
approved tests or alternative checks and obtain implementation approval before
applying job or schedule changes. A dry run alone is not approval to apply changes.

Perform all edits and validation, including unrelated formatting cleanup and
schedule spreading, before requesting explicit approval for committing, pushing,
or creating a PR. Validate the final combined changes before requesting approval.
Invocation of this skill does not itself approve implementation or publication.
Use an isolated worktree and the inherited Git guardrails for branch setup and
commit requirements rather than treating the command examples as authorization.

## What the tool does

For each SIG it:
- Copies the presubmit job from the source version, setting `always_run: false` and `optional: true`
- Copies the periodic job from the source version, advancing the cron schedule by +1h10m
- Updates the `TARGET` env var and job name to the new k8s version

## Prerequisites

The repo `kubevirt.io/project-infra` must be cloned at:
`$HOME/Projects/github.com/kubevirt.io/project-infra`

## Steps

### 1. Locate the repository

```
REPO=$HOME/Projects/github.com/kubevirt.io/project-infra
```

Verify `$REPO/robots/kubevirt/main.go` exists. If not, stop and tell the user.

### 2. Build the tools

Build the kubevirt robot:
```bash
cd $REPO/robots/kubevirt && go build -o /tmp/kubevirt-robot .
```

If the build fails, report the error and stop.

### 3. Determine the k8s version

- If `$ARGUMENTS` is provided, use it as the target k8s version (e.g. `1.36`).
- If not provided, tell the user the tool will auto-detect the latest k8s release from GitHub (requires a GitHub token or public API access).

### 4. Create a feature branch

Create a new branch from the current HEAD:
```bash
git checkout -b ci/copy-lanes-k8s-$VERSION
```

### 5. Run copy jobs

Set the job config paths:
```
PRESUBMITS=$REPO/github/ci/prow-deploy/files/jobs/kubevirt/kubevirt/kubevirt-presubmits.yaml
PERIODICS=$REPO/github/ci/prow-deploy/files/jobs/kubevirt/kubevirt/kubevirt-periodics.yaml
```

Verify both files exist.

**Dry run first** (always):
```bash
/tmp/kubevirt-robot copy jobs \
  --job-config-path-kubevirt-presubmits="$PRESUBMITS" \
  --job-config-path-kubevirt-periodics="$PERIODICS" \
  --k8s-release-semver="$VERSION" \
  --dry-run=true \
  --github-token-path=""
```

Show the user a summary of what jobs will be created (parse the log output for "Copying source to target job" and "Target job exists" lines).

If the dry run shows all target jobs already exist, tell the user and stop.

**Apply changes**:
```bash
/tmp/kubevirt-robot copy jobs \
  --job-config-path-kubevirt-presubmits="$PRESUBMITS" \
  --job-config-path-kubevirt-periodics="$PERIODICS" \
  --k8s-release-semver="$VERSION" \
  --dry-run=false \
  --github-token-path=""
```

### 6. Validate configuration

Run `hack/checkconfig.sh` from the repo root to validate the Prow configuration. This script runs `checkconfig` via podman and needs `GOOGLE_APPLICATION_CREDENTIALS` set in the environment.

```bash
cd $REPO && ./hack/checkconfig.sh
```

If validation fails, report the error and stop. Do not commit invalid configuration.

### 7. Remove tool-generated unrelated changes

The `kubevirt copy jobs` tool re-marshals the entire YAML file, which can introduce unrelated formatting changes (collapsed multi-line strings, added `name: ""` fields, removed comments, whitespace changes).

Check for and remove only unrelated changes introduced by this tool run:

1. Run `git diff` and inspect the diff for changes outside the newly added job blocks.
2. Look specifically for:
   - Multi-line shell commands collapsed into single lines (YAML block scalars flattened)
   - Spurious `name: ""` fields added to existing containers
   - Removed comments (e.g. `# TODO:` lines)
   - Whitespace-only changes in existing job definitions
3. Fix each unrelated change by restoring the original formatting using the Edit tool.
4. Keep the changes uncommitted until final validation and publication approval.

### 8. Spread periodic job schedules

Run the `periodic-jobs spread` tool to redistribute cron schedules evenly and avoid load clustering:

```bash
cd $REPO && go run ./cmd/periodic-jobs spread --verbose
```

Keep the schedule changes uncommitted as well.

### 9. Generate gantt chart and validate final changes

Generate the Mermaid gantt chart showing the spread schedule:
```bash
cd $REPO && go run ./cmd/periodic-jobs gantt
```

Capture the gantt chart output (the full ```` ```mermaid ... ``` ```` block).

Re-run `hack/checkconfig.sh`, run `yamllint` on both changed YAML files, and
inspect the final diff. Summarize the lane changes, schedule impact, and validation
results, including failed or skipped checks. Request explicit approval for the
intended commits, push, and draft PR creation, and wait.

### 10. Commit and create draft PR after approval

Perform only the approved publication actions. Stage only the intended job config
changes and use conventional commit messages, for example
`ci: copy SIG test lanes for k8s $VERSION provider`. Include the required DCO
sign-off and AI-assistance trailer from the inherited commit policy.

Create a draft PR using `gh pr create`. Push to the `fork` remote first:
```bash
git push fork ci/copy-lanes-k8s-$VERSION
```

Then create the PR:
```bash
gh pr create --repo kubevirt/project-infra --draft \
  --title "ci: copy SIG test lanes for k8s $VERSION provider" \
  --body "$(cat <<'PREOF'
## Summary

- Add presubmit and periodic e2e job definitions for k8s $VERSION (copied from previous version)
- New presubmits: `always_run: false`, `optional: true`
- Spread periodic job cron schedules to minimize concurrent execution

## Periodic Job Schedule

<GANTT_CHART_OUTPUT>

## Lane Migration Reminders

- Promote presubmits later with `kubevirt require presubmits` once the provider is integrated
- When merging: `/hold` the PR, scale down `statusreconciler`, then `/unhold`
- After merge: use `migratestatus` to retire old job statuses if removing old lanes

See `docs/lane-migration-process.md` for full details.
PREOF
)"
```

Replace `<GANTT_CHART_OUTPUT>` with the actual gantt chart Mermaid block captured above. Replace `$VERSION` with the actual version in the title and body.

Report the PR URL to the user.

### 11. Cleanup

```bash
rm -f /tmp/kubevirt-robot
```

## Rules

- Always do a dry run of `copy jobs` first and show results before applying.
- Never modify files outside the project-infra repository.
- If `--github-token-path` is not available, pass an empty string — the `--k8s-release-semver` flag bypasses the GitHub API call for release detection.
- Always validate with `checkconfig.sh` before committing.
- Always create PRs as drafts.
- Always push to the `fork` remote, not `origin`.
- Clean up the built binary after completion.
