
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

This directory contains the KubeVirt organization's collection of repositories. KubeVirt is a Kubernetes add-on that enables virtualization functionality, allowing users to run virtual machines alongside containers in Kubernetes clusters. Each subdirectory is an independent Git repository — check each repo's README.md and AGENTS.md for specific guidance.

## Creating Issues

### SIG and WG Labelling

When a user mentions a **SIG** or **WG** in the context of creating an issue, apply the label via Prow command (`/sig {name}` or `/wg {name}`) in the issue body or as a comment.

| SIG labels | WG labels |
|---|---|
| `sig/api`, `sig/buildsystem`, `sig/ci`, `sig/compute`, `sig/control-plane`, `sig/documentation`, `sig/network`, `sig/observability`, `sig/release`, `sig/scale`, `sig/security`, `sig/storage`, `sig/testing` | `wg/aie`, `wg/arch-arm`, `wg/arch-s390x`, `wg/code-quality` |

Authoritative source: `project-infra/github/ci/prow-deploy/files/labels.yaml`

### Clustered Failures

A **clustered failure** is defined as a failure that occurs more than five times.

### Analyzing Build and PR Failures

When analysis produces a **build URL** or **pull request URL** as an intermediate result, use the corresponding skill:
- **Build URL** → `analyze-build` skill
- **PR URL** → `analyze-pr-builds` skill

## CI/CD and Test Stability Reports

- **TestGrid**: https://testgrid.k8s.io/kubevirt
- **CI Health Dashboard**: https://kubevirt.io/ci-health/#kubevirtkubevirt
- **CI Search**: https://search.ci.kubevirt.io/
- **Flakefinder**: https://storage.googleapis.com/kubevirt-prow/reports/flakefinder/kubevirt/kubevirt/index.html
- **Flake stats (14d)**: https://storage.googleapis.com/kubevirt-prow/reports/flakefinder/kubevirt/kubevirt/flake-stats-14days.html
- **Presubmit report**: https://storage.googleapis.com/kubevirt-prow/reports/e2ejobs/kubevirt/kubevirt/presubmits.html
- **Periodic report**: https://storage.googleapis.com/kubevirt-prow/reports/e2ejobs/kubevirt/kubevirt/periodics.html
