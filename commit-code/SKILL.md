---
name: commit-code
description: Commits the content of the current workspace and pushes it to the remote repository, including modification summary, local Git author email, and co-author information.
---

When committing and pushing code, always include:

1. **Modification summary**: Concisely summarize the changes in the current workspace (such as feature development, bug fixes, document updates, file additions/deletions, etc.), no more than 50 characters for the brief summary, with detailed descriptions if necessary.

2. **Local Git author email**: Sign with the author email configured in the local Git, using the format "Signed-off-by: [local-git-user-email]". The email must be obtained from the local Git configuration (command: git config user.email), not manually entered.

3. **Co-Author information**: Fixed format "Co-Authored-By: <model-name> <noreply@anthropic.com>", where <model-name> must be replaced with the name of the currently used model (e.g., Claude 3.5 Sonnet, Claude 3 Opus).

Execution steps (in order):

1. Scan the workspace changes (git status, git diff --name-only) and generate a modification summary.

2. Extract the local Git author email via the command "git config user.email".

3. Replace <model-name> in the co-author format with the current model name.

4. Execute git add ., then git commit with the standard format, and finally git push to the remote repository.

Commit message template (mandatory):

type: concise modification summary

Detailed changes (optional):

- Change 1

- Change 2

Signed-off-by: local-git-user@example.com

Co-Authored-By: <model-name> <noreply@anthropic.com>
