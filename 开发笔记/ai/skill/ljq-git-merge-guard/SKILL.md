---

name: ljq-git-merge-guard

description: Guards Git synchronization and conflict resolution so feature work is preserved while target-branch changes are integrated. Use for merge, pull, sync, rebase, cherry-pick, branch update, conflict resolution, or any request that could combine Git histories.

---

  

# Git Merge Guard

  

Treat a conflict as competing intent, never as a whole-file choice. Preserve the feature branch's completed behavior while integrating the target branch's compatible structure and fixes.

  

## Required workflow

  

1. Confirm the repository, current branch, target ref, remotes, upstream, and worktree status. Do not switch, reset, or synchronize a dirty shared worktree.

2. Run the preflight before any history-changing command:

  

   ```powershell

   pwsh -File scripts/git-merge-guard.ps1 preflight origin/main

   ```

  

3. For a shared feature branch, prefer merging the latest target into the feature branch and later performing an ordinary push. Do not rebase or force-push unless the user explicitly authorizes that strategy.

4. Preview ancestry, unique commits, and likely conflicts. Read the feature implementation, target implementation, relevant commits, callers, specifications, and tests before deciding.

5. Start the merge without auto-committing. If conflicts occur, capture them:

  

   ```powershell

   git merge --no-ff --no-commit origin/main

   pwsh -File scripts/git-merge-guard.ps1 capture origin/main

   ```

  

6. Complete `.git/codex-merge-decisions.md` for every conflicted file. Record feature intent, target intent, the semantic resolution, and its verification. The verification gate rejects unresolved `TODO` entries.

7. Resolve individual hunks semantically. Port feature behavior onto the target's current structure when necessary. Do not accept an entire side merely because it is newer.

8. If intent is ambiguous or incompatible, stop and report the decision needed. Abort the unsafe merge if required; never guess by overwriting.

9. Build and run the critical feature scenarios, inspect the final diff against both recorded heads, then commit the merge.

10. Run the final gate after committing:

  

   ```powershell

   pwsh -File scripts/git-merge-guard.ps1 verify origin/main

   ```

  

11. Push only when authorized. Recheck the remote SHA, ancestry, pipeline, and MR state after the push.

  

## Hard stops

  

- Never resolve conflicts with whole-file `checkout --ours`, `checkout --theirs`, `restore --ours`, or `restore --theirs`. Documentation does not make an overwrite safe; resolve the individual hunks semantically.

- Never use `reset --hard`, shared-branch rebase, or force-push as a synchronization shortcut.

- Never mark a merge complete if either recorded pre-merge head is not an ancestor of the result.

- Never claim success when unique feature behavior, target fixes, build verification, or critical scenarios are unverified.

  

## Decision rule

  

The best resolution preserves both valid intents. When both cannot coexist, choose only from verified product requirements and state the discarded behavior explicitly. Lack of evidence is a stop condition, not permission to prefer `ours` or `theirs`.

  

## Completion checklist

  

- Baseline JSON exists under `.git/codex-merge-guard.json`.

- Every conflict has a completed decision entry with no `TODO`.

- Feature and target pre-merge heads are both ancestors of the result.

- Worktree is clean and `git diff --check` passes.

- Feature behavior and target compatibility checks pass.

- Push uses an ordinary update unless the user explicitly approved otherwise.