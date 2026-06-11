---
name: prow-diagnose
description: Diagnose Prow CI build failures for a pull request in the kubevirt.io ecosystem. Pass the PR number as argument.
argument-hint: <pr-number>
allowed-tools: mcp__prow__diagnose_pr_failures, mcp__prow__diagnose_pr_build_status, mcp__prow__get_latest_prow_build_for_pr, mcp__prow__get_test_failures_from_artifacts, mcp__prow__get_build_logs, mcp__prow__get_job_logs, mcp__prow__get_recent_job_status
---

Diagnose Prow CI failures for PR $ARGUMENTS in the kubevirt.io ecosystem.

If no PR number is provided, ask the user before proceeding.

## Steps

1. Use `diagnose_pr_build_status` to get an overview of the PR's CI state
2. Identify which jobs are failing or stuck pending
3. For each failing job, retrieve build logs and identify the root cause
4. Extract specific test failures from artifacts where applicable
5. Determine if failures look like flaky tests or genuine regressions

## Summary to provide

- Which jobs failed and why
- Whether failures appear flaky (intermittent, unrelated to the PR's changes) or real
- Suggested next action: retest, fix code, quarantine test, or investigate infra issue
