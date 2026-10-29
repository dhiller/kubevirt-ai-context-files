# Markdown Guidelines

## Scope

Apply these guidelines when creating or editing Markdown documentation, including agent guidance.
Follow repository-specific instructions and formatter configuration first. Markdown should be readable
both as rendered content and as plain-text source in a monospace editor.

## Readable Source

- Align table pipes vertically and pad cells consistently to the widest source cell in each column.
  Include a space between cell content and its surrounding pipes, and preserve column-alignment markers.
- Format tables correctly when writing them; do not leave source alignment for the user to repair.
- Use a header and delimiter row, outer pipes, and at least three dashes per delimiter cell.
- Preserve content, row order, inline code, links, escaped pipes, and alignment semantics when formatting.
  Do not split cells across source lines merely to satisfy a line-length limit.
- Prefer reference-style links or shorter descriptions when long URLs make tables unwieldy, provided this
  follows the document's existing style and does not obscure essential information.
- Use consistent heading levels, blank lines around headings/lists/fenced blocks, and language identifiers
  on fenced code blocks when the language is known.
- Preserve existing prose wrapping and list conventions unless the task or repository formatter requires
  a change. Avoid trailing whitespace; preserve intentional Markdown hard breaks.

Example of readable table source:

```markdown
| Branch       | Freeze     | Thaw     |
| ------------ | ---------- | -------- |
| main         | Selective  | Ordinary |
| release-1.11 | Ineligible | Ordinary |
| release-1.10 | Ordinary   | Ordinary |
```

## Manual Formatting and Tools

Manual formatting is acceptable for small changes. Use a formatter for larger documents or many tables
when it improves consistency and efficiency. A formatter is optional; readable source is not.

Use the repository's established tool and configuration. If none exists, suitable Fedora/Linux options
are Prettier or mdformat with its mdformat-gfm plugin. Do not introduce a repository-wide dependency,
configuration, or formatting sweep merely to align tables in one document.

Prettier can align tables while preserving prose wrapping and leaving fenced code content untouched:

```bash
npx prettier --write document.md --prose-wrap preserve --embedded-language-formatting off
npx prettier --check document.md --prose-wrap preserve --embedded-language-formatting off
```

mdformat requires the mdformat-gfm plugin for GitHub Flavored Markdown tables. To preserve paragraph
wrapping and use consecutive ordered-list numbering:

```bash
mdformat --wrap keep --number document.md
mdformat --check --wrap keep --number document.md
```

Do not enable mdformat's `--compact-tables`: it removes the padding used for readable source alignment.
Use mdformat-gfm rather than the archived mdformat-tables plugin. Install any additional syntax plugins
needed by the document before formatting it. Pin formatter/plugin versions for repeatable automation.

Both tools can normalize the whole document, not just tables. Review the diff and avoid unrelated churn.
A Markdown linter checks rules but is not a substitute for source formatting or visual inspection.

## Validation

- Inspect modified tables in raw source, including inline markup that affects source column widths.
- Review the rendered output when available, particularly for escaped pipes, links, and code spans.
- Run the repository's Markdown checks and formatter check mode when configured.
- Inspect the diff for accidental content changes, unrelated rewrapping, and altered code examples.
- Run `git diff --check` for tracked changes; account for intentional Markdown hard breaks rather than
  blindly stripping whitespace.
- Keep formatting-only changes separate from substantive edits where practical. Do not claim a Markdown
  linter verifies links or formatting properties that its configured rules do not check.
