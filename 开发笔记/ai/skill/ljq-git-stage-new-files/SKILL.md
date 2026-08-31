---
name: git-stage-new-files
description: Use when checking whether newly created or untracked files in the current Git project have been added to the index, especially before finishing AI-generated code changes without committing.
---

# Git Stage New Files Check

## Overview

Check the current Git repository for new files and report whether each new file is already staged. Do not commit, do not push, and do not use broad staging commands such as `git add .`, `git add -A`, or `git add *`.

## Workflow

1. Confirm the working directory is inside a Git repository:

```bash
git rev-parse --show-toplevel
```

If this fails, tell the user the current directory is not a Git repository and ask which project subdirectory should be checked.

2. Inspect status with all untracked files shown:

```bash
git status --porcelain=v1 -uall
```

3. Classify new files from the two status columns:

| Status | Meaning | Report |
|---|---|---|
| `?? path` | New untracked file | Not staged |
| `A  path` | New file staged in index | Staged |
| `AM path` | New file staged, then modified again | Partially staged; working-tree changes remain |
| `AD path` | New file staged, then deleted in working tree | Staged addition is stale; needs user decision |
| `R  old -> new` with staged add/delete semantics | Rename staged | Staged rename; mention both paths |

Modified or deleted tracked files (`M`, `D`) are not "new files"; mention them only as separate context if useful.

4. Output a concise report:

```markdown
**Git New File Check**
- Repository: `<repo-root>`
- New files staged: `<count>`
- New files not staged: `<count>`
- Result: `<all staged | needs git add | no new files>`

Staged new files:
- `<path>`

Not staged new files:
- `<path>`
```

5. If the user asked to ensure new files are added, stage only explicit paths:

```bash
git add -- path/to/new-file another/new-file
```

Then rerun `git status --porcelain=v1 -uall` and report the result.

## Safety Rules

- Never run `git commit` for this skill unless the user explicitly asks for a commit in the current turn.
- Never run `git push`.
- Never use `git add .`, `git add -A`, `git add *`, or shell-expanded bulk staging.
- Do not stage ignored files. If a needed generated file is ignored, stop and ask.
- If multiple Git repositories exist under a parent directory, check the repository containing the current working directory. If the current directory is not a repository, ask the user which repository to inspect.

## Common Mistakes

- Treating `M path` as a new file. It is a tracked modification, not an untracked/new add.
- Reporting only `git diff --cached --name-only`; that misses untracked files.
- Using `git status --short` without `-uall`; collapsed untracked directories can hide individual files.
- Staging everything from the repo root. Always stage explicit paths only.
