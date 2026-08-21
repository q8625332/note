---
name: local-code-links
description: Use when reporting local repository files, source code, tests, SQL, logs, or documents that the user must open in Pi's side preview; especially when a raw path or ordinary Markdown link does not open on click.
---
# Local Code Links

## Contract

When the user needs to open a local repository file in Pi's side preview, every delivered file reference MUST be a Markdown link in this shape:

```markdown
[File name](/D:/absolute/forward/slash/path/File.java:42)
```

The leading slash, forward slashes, and line number are required. Violating the exact link shape is a failure even when the underlying path is correct.

## Procedure

1. Resolve the repository root and file path to an absolute Windows path.
2. Convert `D:\...\file` to `/D:/.../file`.
3. Use a real line number when pointing to a symbol; use `:1` for a whole-file reference.
4. Put the path directly in Markdown link target syntax.
5. If the path contains spaces or parentheses, wrap the target in angle brackets:

```markdown
[Report](</D:/Project Folder/docs/Report.md:1>)
```

6. For several files, provide one link per file. Add a short description after each link, not instead of the link.

## Do Not Use

- Raw `D:\...` or `D:/...` paths as the only reference.
- Relative targets such as `[file](src/main/File.java)`.
- `file:///...` URIs.
- `vscode://file/...` URIs.
- Backslashes in the Markdown target.
- Links without a line number when a line can be supplied.
- Code fences around the Markdown link.

## Quick Check

Before sending, verify each target:

- Starts with `/` followed by a Windows drive, such as `/D:/`.
- Contains only forward slashes.
- Resolves to an existing file.
- Ends with `:line` (or `:1`).
- Is not wrapped in backticks.

## Common Failure

A normal Markdown link may render as text but fail to trigger Pi's side preview. The fix is not a `file://` or IDE URI; normalize the absolute path to Pi's `/D:/...:line` form.
