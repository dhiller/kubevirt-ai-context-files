---
name: kubevirt-pr
description: Create a pull request in the kubevirt.io ecosystem following project conventions — conventional commits, fork-based workflow, and proper PR description.
allowed-tools: Bash(git *), Bash(gh *)
---

Create a PR for the current changes following kubevirt.io conventions.

## Commit message format

Use conventional commits: `type(scope): description`

- **type**: `feat`, `fix`, `chore`, `refactor`, `test`, `docs`, `ci`
- **scope**: the component or subsystem (e.g. `cannier`, `buildlog`, `flakefinder`, `labels-checker`)
- **description**: imperative mood, lowercase, no trailing period

Example: `feat(buildlog): add TIMEOUT as error keyword`

## PR workflow

1. Verify all commits on the branch follow the conventional commit format
2. Push the branch to the fork remote (`origin`): `git push origin <branch>`
3. Create the PR against the upstream `kubevirt/project-infra` — not the dhiller fork:
   `gh pr create --repo kubevirt/project-infra`
4. The PR title must follow the same conventional commit format as the commits
5. PR body must include:
   - What changed and why (not just what)
   - Related issues using `Fixes #NNN` or `Refs #NNN` if applicable
   - Check `.github/PULL_REQUEST_TEMPLATE.md` and use its structure if present

## After creating

Report the PR URL and remind the user to watch Prow CI results with `/prow-diagnose <pr-number>`.
