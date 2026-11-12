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

## Prow Metrics

Prow exports 75+ Prometheus metrics. Key metrics are in `kubernetes-sigs/prow/pkg/kube/metrics.go` and component-specific files.

### Core ProwJob Metrics

**`prowjobs`** (Gauge) — Labels: `job_namespace`, `job_name`, `type`, `state`, `org`, `repo`, `base_ref`, `cluster`, `retest`. Number of prowjobs in the system.

**`prowjob_state_transitions`** (Counter) — Same labels. Number of prowjobs transitioning to each state.

**`prow_job_runtime_seconds`** (Histogram, in `pkg/metrics/prowjobs/collector.go`) — Labels: `job_namespace`, `job_name`, `type`, `last_state`, `state`, `org`, `repo`, `base_ref`. Time between ProwJob state transitions.

### Other Component Metrics

- **Tide** (`pkg/tide/tide.go`): `pooledprs`, `updatetime`, `merges`, `tidepoolerrors`, `syncdur`, `retests`, `poolmissingprs`, `poolpendingprs`, `poolsuccessfulprs`
- **Crier** (`pkg/crier/metrics.go`): `crier_report_latency`, `crier_reporting_results`
- **GitHub** (`pkg/github/ghmetrics/ghmetrics.go`): `github_token_reset`, `github_token_usage`, `github_request_duration`, `github_request_wait_duration_seconds`, `ghcache_responses`
- **Sinker** (`cmd/sinker/main.go`): `sinker_pods_existing`, `sinker_loop_duration_seconds`, `sinker_pods_removed`, `sinker_prow_jobs_existing`, `sinker_prow_jobs_cleaned`
- **Webhook** (`pkg/githubeventserver/metrics.go`): `prow_webhook_counter`, `prow_webhook_response_codes`, `prow_plugin_handle_duration_seconds`, `prow_plugin_handle_errors`
- **InRepoConfig Cache** (`pkg/config/cache.go`): `inRepoConfigCache_lookups`, `inRepoConfigCache_hits`, `inRepoConfigCache_misses`, `inRepoConfigCache_GetProwYAML_duration`

### Useful PromQL Queries

```promql
sum by (org, repo, type) (prowjobs{state="pending"})      # pending jobs by org/repo/type
sum by (state) (prowjobs)                                   # all jobs by state
rate(prowjob_state_transitions{state="success"}[5m])        # job completion rate
rate(prowjob_state_transitions{state="failure"}[5m])        # failed job rate
rate(prowjob_state_transitions[5m])                         # total job throughput
```
