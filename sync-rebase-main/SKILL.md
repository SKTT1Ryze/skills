---
name: sync-rebase-main
description: Synchronizes the latest code from the remote repository's main branch and rebases it onto the current local branch, including workspace status check, remote fetch, rebase execution, and conflict handling guidance.
---

When synchronizing and rebasing the remote main branch, follow these key principles:
1. **Workspace safety first**: Always check for uncommitted changes before starting to avoid data loss.
2. **Remote branch clarity**: Use the standard `origin/main` (or `origin/master` if applicable) as the remote target branch.
3. **Conflict handling guidance**: Provide clear steps for resolving conflicts if they arise during rebase.

Execution steps (in order):

1. **Check workspace status**:
   - Run `git status` to inspect the current workspace.
   - If there are uncommitted changes:
     - Prompt the user to either commit them first, or stash them with `git stash` (can be restored later with `git stash pop`).
   - Proceed only when the workspace is clean (no uncommitted changes).

2. **Fetch latest remote changes**:
   - Execute `git fetch origin` to retrieve the latest code from the remote repository without merging.

3. **Perform rebase**:
   - Run `git rebase origin/main` to rebase the current local branch onto the latest remote main branch.
   - (Note: Replace `origin/main` with `origin/master` if the remote default branch uses "master".)

4. **Handle rebase conflicts (if any)**:
   - If Git reports conflicts:
     1. Inform the user which files have conflicts.
     2. Instruct the user to manually edit the conflicting files to resolve the conflicts.
     3. After resolving, run `git add <resolved-file(s)>` to stage the fixes.
     4. Execute `git rebase --continue` to proceed with the rebase.
   - If the user wants to abort the rebase: Run `git rebase --abort` to return to the state before the rebase started.

5. **Verify rebase result**:
   - After rebase completes, run `git log --oneline -5` to confirm the commit history is correctly rebased.

Key notes:
- If you are unsure about the remote branch name, run `git branch -r` to list all remote branches.
- It is recommended to create a backup branch before rebase (e.g., `git branch backup-before-rebase`) for safety.
