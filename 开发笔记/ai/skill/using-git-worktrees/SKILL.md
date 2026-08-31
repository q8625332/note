---
name: using-git-worktrees
description: Use when starting feature work that needs isolation from current workspace or before executing implementation plans - ensures an isolated workspace exists via native tools or git worktree fallback
---

# Using Git Worktrees

Ensure work happens in an isolated workspace. Prefer native worktree tools, fall back to git.

**Announce at start:** "I'm using the using-git-worktrees skill to set up an isolated workspace."

## Step 0: Detect Existing Isolation
```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
```
If `GIT_DIR != GIT_COMMON` (and not a submodule): already in linked worktree, skip creation.
Submodule guard: `git rev-parse --show-superproject-working-tree` returns path → treat as normal repo.

## Step 1: Create Isolated Workspace
1a. Prefer native worktree tool (EnterWorktree, /worktree, --worktree).
1b. Git fallback only if no native tool:
```bash
ls -d .worktrees 2>/dev/null || ls -d worktrees 2>/dev/null
git check-ignore -q .worktrees || git check-ignore -q worktrees   # MUST be ignored
git worktree add "$LOCATION/$BRANCH_NAME" -b "$BRANCH_NAME"
```

## Step 2: Project Setup
Auto-detect package.json/Cargo.toml/requirements.txt/pyproject.toml/go.mod.

## Step 3: Verify Clean Baseline
Run project tests. If fail, report and ask.

## Red Flags
- Never create worktree when Step 0 detects existing isolation.
- Never use `git worktree add` when native tool available.
- Always verify directory ignored for project-local worktree.