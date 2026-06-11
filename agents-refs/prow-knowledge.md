# Prow Knowledge

## Approve Plugin and OWNERS File Filters

The Prow approve plugin supports regex filters in OWNERS files. Fixed in [PR #29970](https://github.com/kubernetes/test-infra/pull/29970) (merged and deployed as `v20240517-ea10bd814`), resolving [issue #7690](https://github.com/kubernetes/test-infra/issues/7690).

The fix patched several methods without a massive refactoring:
- `Approvers.UnapprovedFiles`
- `Owners.GetSuggestedApprovers`
- `RepoOwners.entriesForFile` (fixed `NoParentOwners` to only stop parent traversal when at least one approver was found via filter match)

Example of working filtered OWNERS file:
```yaml
filters:
  "\\.yaml$":
    approvers:
      - alice
  "\\.go$":
    approvers:
      - bob
```

## Triggering CI Lanes on PRs

- **`/test {test-lane-name}`** — trigger a specific lane (e.g. `/test pull-kubevirt-e2e-k8s-1.35-sig-compute`)
- **`/retest`** (no args) — retrigger all failed required jobs
- Do NOT use `/retest {lane-name}` — that is not a valid Prow command
- Only retrigger **required** lanes that failed — optional lanes don't block merge

## ProwJob Status States (from prow source `prowjobs/v1/types.go`)

- **scheduling**: Job created, waiting to be scheduled.
- **triggered**: Job scheduled but not yet running.
- **pending**: Job is currently running, waiting to finish.
- **success**: Completed without error (exit 0).
- **failure**: Completed with errors (exit non-zero).
- **aborted**: Prow killed the job early (e.g. new commit pushed).
- **error**: Job could not schedule (e.g. bad config).
