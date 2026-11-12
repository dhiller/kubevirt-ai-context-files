# Jira Guidelines

These are local workflow defaults for agents working in this directory tree,
not organization-wide Jira policy.

## Defaults for New Issues

Propose these values whenever creating a Jira issue, unless the user specifies
different values:

| Field        | Default              |
| ------------ | -------------------- |
| Component    | Upstream CI Platform |
| Label        | operations           |
| Story points | 5                    |
| Priority     | Major                |
| Severity     | Moderate             |

Include the defaults in the creation preview; do not apply them silently.
Story points are a proposed default, not an independently established estimate.
Do not infer other defaults, such as project, issue type, assignee, or parent,
from this table. Resolve missing context before asking for confirmation.

Verify that the destination project supports the requested component and field
values. Resolve custom fields, including story points and severity, using that
site's metadata rather than assuming field IDs. If a default is unavailable,
explain the mismatch and ask the user how to proceed instead of silently
substituting or omitting it.

## Present Changes and Obtain Confirmation

Before creating an issue or changing an existing issue:

1. Read the current issue when updating it, and prepare the complete proposed
   change without writing to Jira.
2. Present a concise preview. For creation, show the project, issue type,
   summary, description, and all proposed fields, including the defaults above.
   For updates, identify the issue and show each changed field's current and
   proposed values; show proposed description or comment text when applicable.
3. Explicitly ask the user to confirm the presented proposal and wait for their
   approval. A request to create or update an issue starts this preview workflow;
   it does not replace confirmation of the actual proposal.
4. Apply only the confirmed changes. If the proposal changes, obtain confirmation
   again before writing. Read-only lookups do not require this confirmation.
5. Verify the saved values and report the issue link and outcome. Do not claim a
   custom field was saved merely because it was sent in the request.

## Preserve Existing Values

The table supplies creation defaults, not permission to normalize existing
issues. On updates, leave unrelated fields unchanged. Preserve existing labels
and components when adding values, and show removals explicitly in the preview.
When an API replaces a multi-value field rather than appending to it, send the
full confirmed list so existing values are not accidentally discarded.

## Keep Local Guidance Local

Do not offer to upload local guidelines or agent configuration to Atlassian
Artifacts. In particular, avoid asking "Would you like the guideline uploaded to
Atlassian Artifacts?" after a local documentation change. Keep these files local
unless the user explicitly requests publication or an upload.
