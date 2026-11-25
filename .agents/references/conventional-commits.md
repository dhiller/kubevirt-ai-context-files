# Conventional Commits

All commit messages MUST follow the [Conventional Commits specification](https://www.conventionalcommits.org/en/v1.0.0/).

## Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Common Types

- **`fix`** - patches a bug (correlates with PATCH in SemVer)
- **`feat`** - introduces a new feature (correlates with MINOR in SemVer)
- **`docs`** - documentation only changes
- **`style`** - formatting, missing semicolons, etc; no code change
- **`refactor`** - code change that neither fixes a bug nor adds a feature
- **`perf`** - code change that improves performance
- **`test`** - adding or correcting tests
- **`build`** - changes to build system or dependencies
- **`ci`** - changes to CI configuration files and scripts
- **`chore`** - other changes that don't modify src or test files
- **`revert`** - reverts a previous commit

## Breaking Changes

- Append `!` after type/scope (e.g., `feat!:` or `feat(api)!:`)
- Include `BREAKING CHANGE:` in footer

## AI Agent Trailer

AI-assisted commits must include exactly one `Assisted-by:` trailer in the footer:

```text
Assisted-by: <Agent Name> <Model Version> <email>
```

Use the actual agent name and model version. Do not guess these values; resolve
them before committing.

By default, use an address on the model provider's domain, unless the user or
applicable repository guidance explicitly specifies another address. A reasonable
address such as `noreply@openai.com` for an OpenAI model is sufficient; checking
whether the address is documented or accepts mail is not required. Keep the
actual agent name and model version in the trailer, even when the email uses the
model provider's domain.

If the model provider's domain is unknown and no explicit override is available,
ask the user to designate an attribution email before committing.

Use `Assisted-by:` rather than an additional `Co-Authored-By:` trailer for AI
attribution. Preserve attribution belonging to human contributors.

This trailer does not replace a required DCO `Signed-off-by:` trailer.
Follow the repository's DCO requirements separately.

## No Issue/PR References in Commit Messages

This is a local workflow rule, not a claim about every repository's policy.

Do not include issue or pull-request references anywhere in commit messages,
including the subject, body, and trailers. This includes shorthand references
such as `#123` or `owner/repo#123`, issue/PR URLs, and closing phrases such as
`Fixes #123`.

Put these references in the PR description instead. Neutral wording such as
`Related to #123` is also prohibited in commit messages.

Conventional Commit types such as `fix:` remain valid.

## DCO Sign-off

Some repositories require DCO sign-off (`--signoff` / `-s` flag on `git commit`). Check the repository's own `AGENTS.md` for sign-off requirements.

## Examples

```
feat: add s390x cluster support to infrastructure diagram

docs: update AGENTS.md with conventional commits rule

fix(prow): correct duplicate subgraph IDs in Mermaid diagram

feat!: change API endpoint structure

BREAKING CHANGE: /api/v1 endpoints moved to /api/v2
```
