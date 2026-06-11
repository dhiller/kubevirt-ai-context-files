
# AGENTS.md

This file provides guidance to AI agents such as Gemini or Claude when working with code in the repositories located in this directory.

## Files containing context for AI agents in this directory tree

Files in any of the subdirectories of this directory named `AGENTS.md` contain
information applying to any agent visiting that directory. Files named after the
name of an agent, such as `GEMINI.md` or `CLAUDE.md` contain information for that
specific agent only.

## General Workflow

For general workflow rules (branching, conventional commits, draft PRs, git remote configuration, validation before committing, PR descriptions, Jira-to-PR sync), see the [top-level AGENTS.md](../AGENTS.md).

## Overview

This directory contains the KubeVirt organization's collection of repositories. KubeVirt is a Kubernetes add-on that enables virtualization functionality, allowing users to run virtual machines alongside containers in Kubernetes clusters.

## Repository Structure

The KubeVirt organization consists of multiple repositories, each serving specific purposes:

### Core Components
- **kubevirt/**: Main KubeVirt project - core virtualization functionality and controllers
- **containerized-data-importer/**: CDI - handles VM disk image import/cloning/upload
- **kubevirtci/**: CI/CD infrastructure and cluster deployment tools
- **project-infra/**: Organization-wide infrastructure automation and tooling

### Operators and Extensions
- **hyperconverged-cluster-operator/**: HCO - manages the complete KubeVirt stack
- **ssp-operator/**: SSP - provides templates and preference management  
- **hostpath-provisioner-operator/**: HPP - local storage provisioning for VMs
- **csi-driver/**: CSI driver for VM disk management
- **cdi-operator/**: CDI operator for managing data import functionality

### Client Tools and Libraries
- **kubectl-virt-plugin/**: virtctl CLI tool for VM management
- **client-python/**: Python client library for KubeVirt APIs
- **kubevirt.core/**: Ansible collection (referenced in main README)

### Templates and Examples
- **common-instancetypes/**: Standard VM instance type definitions
- **common-templates/**: Common VM templates for various operating systems
- **containerdisks/**: Container disk images for quick VM deployment

### Documentation and Community
- **community/**: Community governance, design proposals, and meeting notes
- **kubevirt.github.io/**: Main website and documentation
- **user-guide/**: End-user documentation and tutorials
- **kubevirt-tutorial/**: Interactive tutorials for learning KubeVirt
- **api-reference/**: API documentation across versions

### Testing and Quality
- **test-benchmarks/**: Performance and benchmark testing
- **must-gather/**: Diagnostic data collection tools
- **qe-tools/**: Quality engineering and testing utilities

## Working with Multiple Repositories

### Repository Navigation
When working in this directory, you're in the organization root containing multiple Git repositories. Each subdirectory is an independent repository with its own:
- Development workflow and build commands
- Git history and branches  
- Issue tracking and pull requests
- Documentation and contribution guidelines

### Key Repository Commands
For the main **kubevirt** repository (most development activity):
```bash
cd kubevirt/
make                    # Full build with manifests
make bazel-test         # Run unit tests
make bazel-build-verify # Verify build and run tests
make generate           # Generate code and manifests
```

For **containerized-data-importer**:
```bash
cd containerized-data-importer/
make                    # Build CDI components
make test               # Run tests
make cluster-up         # Deploy test cluster
```

### Cross-Repository Development
- Changes often span multiple repositories (e.g., API changes in kubevirt + client updates)
- Each repository has its own release cycle and versioning
- Integration testing may require deploying multiple components together
- CI/CD pipelines in project-infra coordinate cross-repository workflows

### Getting Started
1. Identify the specific repository for your work area
2. Navigate to that repository directory
3. Check for repository-specific AGENTS.md, README.md, and CONTRIBUTING.md files
4. Follow that repository's specific development setup and commands

### Common Patterns Across Repositories
- Most repositories use Go and follow similar project structure
- Bazel or Make-based build systems are common
- Dockerized development environments for consistency
- Kubernetes controller pattern for operators
- Ginkgo/Gomega testing framework usage
- CI/CD via Prow (see project-infra)

### Cross-Repository Considerations  
- API compatibility between kubevirt and related operators
- Version alignment across the KubeVirt ecosystem
- Documentation updates may need coordination across repos
- Security updates often require changes in multiple repositories

## Domain Knowledge

### Prow

#### ProwJob State Values

ProwJobs have the following possible state values (defined in `kubernetes-sigs/prow/pkg/apis/prowjobs/v1/types.go:56-69`):

- **`scheduling`** - Job has been created and is waiting to be scheduled
- **`triggered`** - Job has been scheduled but is not running yet
- **`pending`** - Job is currently running and we are waiting for it to finish
- **`success`** - Job completed without error (exit 0)
- **`failure`** - Job completed with errors (exit non-zero)
- **`aborted`** - Prow killed the job early (e.g., new commit pushed)
- **`error`** - Job could not schedule (e.g., bad config)

**Important**: The `pending` state means the job is **actively running**, not waiting to run. Jobs waiting to be scheduled are in `scheduling` or `triggered` states.

For Prometheus queries tracking active jobs consuming cluster resources, use:
- `prowjobs{state="pending"}` - currently running jobs
- `prowjobs{state=~"triggered|pending"}` - scheduled + running jobs

#### Prow Metrics

Prow exports 75+ Prometheus metrics across its components. Key metrics are defined in `kubernetes-sigs/prow/pkg/kube/metrics.go` and other component-specific files.

##### Core ProwJob Metrics

**`prowjobs`** (Gauge)
- **Labels:** `job_namespace`, `job_name`, `type`, `state`, `org`, `repo`, `base_ref`, `cluster`, `retest`
- **Description:** Number of prowjobs in the system, grouped by various dimensions
- **Usage:** Primary metric for tracking job counts by state and other attributes

**`prowjob_state_transitions`** (Counter)
- **Labels:** `job_namespace`, `job_name`, `type`, `state`, `org`, `repo`, `base_ref`, `cluster`, `retest`
- **Description:** Number of prowjobs transitioning to each state
- **Usage:** Track job throughput and completion rates

**`prow_job_runtime_seconds`** (Histogram)
- **Labels:** `job_namespace`, `job_name`, `type`, `last_state`, `state`, `org`, `repo`, `base_ref`
- **Description:** Time between ProwJob state transitions
- **Usage:** Measure job execution time and performance

##### Other Component Metrics

**Tide Metrics** (`pkg/tide/tide.go`): `pooledprs`, `updatetime`, `merges`, `tidepoolerrors`, `syncdur`, `retests`, `poolmissingprs`, `poolpendingprs`, `poolsuccessfulprs`

**Crier Metrics** (`pkg/crier/metrics.go`): `crier_report_latency`, `crier_reporting_results`

**GitHub Metrics** (`pkg/github/ghmetrics/ghmetrics.go`): `github_token_reset`, `github_token_usage`, `github_request_duration`, `github_request_wait_duration_seconds`, `ghcache_responses`

**Sinker Metrics** (`cmd/sinker/main.go`): `sinker_pods_existing`, `sinker_loop_duration_seconds`, `sinker_pods_removed`, `sinker_prow_jobs_existing`, `sinker_prow_jobs_cleaned`

**Webhook Metrics** (`pkg/githubeventserver/metrics.go`): `prow_webhook_counter`, `prow_webhook_response_codes`, `prow_plugin_handle_duration_seconds`, `prow_plugin_handle_errors`

**InRepoConfig Cache Metrics** (`pkg/config/cache.go`): `inRepoConfigCache_lookups`, `inRepoConfigCache_hits`, `inRepoConfigCache_misses`, `inRepoConfigCache_GetProwYAML_duration`

See the full metrics list in the Prow codebase under various `metrics.go` files.

##### Useful Queries for Job Load Dashboards

```promql
# Current pending jobs by org/repo/type
sum by (org, repo, type) (prowjobs{state="pending"})

# Global average of pending jobs over time (horizontal line)
avg_over_time((sum(prowjobs{state="pending"}))[$__range:1m])

# All jobs by state
sum by (state) (prowjobs)

# Job completion rate (state transitions to success)
rate(prowjob_state_transitions{state="success"}[5m])

# Failed job rate
rate(prowjob_state_transitions{state="failure"}[5m])

# Jobs running (triggered state)
sum(prowjobs{state="triggered"})

# Total job throughput
rate(prowjob_state_transitions[5m])
```

## Creating Issues

### SIG and WG Labelling

When a user mentions a **SIG** (Special Interest Group) or **WG** (Working Group) in the context of creating an issue, this means the issue should be labelled with the corresponding label. Apply the label by adding a Prow command in the issue body or as a comment.

- For SIGs, use: `/sig {sig-name}`
- For WGs, use: `/wg {wg-name}`

The user may refer to a SIG or WG in various ways (e.g. "compute", "SIG Compute", "sig-compute"). Always map to the canonical label name from the list below.

#### Available SIG labels

| Label | Prow command | Description |
|---|---|---|
| `sig/api` | `/sig api` | Changes in API |
| `sig/buildsystem` | `/sig buildsystem` | Changes in the build system |
| `sig/ci` | `/sig ci` | Related to sig-ci, changes to the CI system |
| `sig/compute` | `/sig compute` | Virtualization (previously `sig/virtualization`) |
| `sig/control-plane` | `/sig control-plane` | Control plane |
| `sig/documentation` | `/sig documentation` | Documentation |
| `sig/network` | `/sig network` | Networking (previously `topic/network`) |
| `sig/observability` | `/sig observability` | Observability |
| `sig/release` | `/sig release` | Release |
| `sig/scale` | `/sig scale` | Scale |
| `sig/security` | `/sig security` | Security (previously `topic/security`) |
| `sig/storage` | `/sig storage` | Storage (previously `topic/storage`) |
| `sig/testing` | `/sig testing` | Testing |

#### Available WG labels

| Label | Prow command | Description |
|---|---|---|
| `wg/aie` | `/wg aie` | AIE working group |
| `wg/arch-arm` | `/wg arch-arm` | ARM architecture working group |
| `wg/arch-s390x` | `/wg arch-s390x` | s390x architecture working group |
| `wg/code-quality` | `/wg code-quality` | Code quality working group |

The authoritative source for these labels is `project-infra/docs/labels.md` and `project-infra/github/ci/prow-deploy/files/labels.yaml`.

### Clustered Failures

A **clustered failure** is defined as a failure that occurs more than five times. When analyzing CI or test failures, only treat a failure as clustered if it has more than five occurrences.

### Analyzing Build and PR Failures

When analyzing failures for an issue and the analysis produces a **build URL** (e.g. a Prow job URL) or a **pull request URL** as an intermediate result, use the corresponding skill as the base for further analysis:

- **Build URL**: Use the `analyze-build` skill to analyze the root cause of the failed Prow job.
- **Pull request URL**: Use the `analyze-pr-builds` skill to analyze all failed builds for the PR.

## CI/CD and Test Stability Reports

The KubeVirt organization provides comprehensive reporting for CI health, test stability, and flake detection:

### Core CI Monitoring
- **TestGrid**: https://testgrid.k8s.io/kubevirt - Stats for periodic and presubmit e2e jobs
- **CI Health Dashboard**: https://kubevirt.io/ci-health/#kubevirtkubevirt - Overall CI health, merge queue stats, days to merge
- **CI Search**: https://search.ci.kubevirt.io/ - Searchable results of prow jobs across the organization

### Test Stability and Flake Detection
- **Flakefinder Reports**: 
  - Main report: https://storage.googleapis.com/kubevirt-prow/reports/flakefinder/kubevirt/kubevirt/index.html - Test failures on merged PRs
  - Flake stats: https://storage.googleapis.com/kubevirt-prow/reports/flakefinder/kubevirt/kubevirt/flake-stats-14days.html - Which flakes hurt most and where
- **Test Execution Reports**:
  - Presubmits: https://storage.googleapis.com/kubevirt-prow/reports/e2ejobs/kubevirt/kubevirt/presubmits.html - E2e jobs and environment settings
  - Periodics: https://storage.googleapis.com/kubevirt-prow/reports/e2ejobs/kubevirt/kubevirt/periodics.html - Periodic job configurations

### Specialized Test Reports
- **Quarantined Tests**: Lists tests marked with QUARANTINED label for flake management
- **Per-Test Execution (6 months)**: Top failing tests per lane for trend analysis  
- **Merge Commit Summary**: Lane failures specifically on merge commits

These reports are essential for:
- Identifying and quarantining flaky tests
- Monitoring CI health trends
- Understanding test failure patterns
- Making data-driven decisions about test stability
- Tracking the impact of quarantined tests on overall CI reliability

## Important Notes

### Working Directory Context
Since this AGENTS.md is in the organization root directory, when working on specific tasks:
1. First identify which repository contains the relevant code
2. Navigate to that repository directory
3. Use repository-specific development commands and workflows
4. Refer to the repository's own AGENTS.md if it exists, or to the repository's 
   agent-specific context file as described in the beginning of this document.

### Repository-Specific Guidance
The main **kubevirt** repository has comprehensive development documentation. Other repositories may have their own specific requirements and should be consulted for detailed guidance on:
- Build and test procedures
- Architecture and component structure
- Contribution workflows
- API compatibility requirements
