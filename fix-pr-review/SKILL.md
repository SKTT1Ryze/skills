---
name: fix-pr-review
description: Fetches the open PR for the current branch, extracts Claude Code bot review comments, fixes all reported issues, and pushes the changes back to the remote repository.
---

Read and address Claude Code bot review comments for the current branch's pull request.

## Key principles

1. **Extract only actionable feedback**: Focus on issues flagged by the Claude Code bot (`claude` author). Ignore CI status comments and coverage reports.
2. **Fix completely**: Address every unresolved issue before committing. Do not partially fix.
3. **Verify before pushing**: Run `cargo lint` (or the project's lint/test command) after fixes to confirm nothing is broken.

## Execution steps (in order)

1. **Find the PR for the current branch**:
   - Run `gh pr list --head $(git branch --show-current) --json number,title,url` to find the open PR.
   - If no PR exists, inform the user and stop.

2. **Fetch Claude bot review comments**:
   - Run `gh pr view <number> --comments` to fetch all comments.
   - Extract comments where `author` is `claude`. These contain the review rounds.
   - From the most recent Claude comment, identify all **unresolved issues** (issues not marked as fixed in a later review round).

3. **Summarize issues to the user**:
   - List each unresolved issue with: file path, line reference, description, and suggested fix.
   - Ask the user to confirm before making changes if the scope is large or ambiguous.

4. **Fix each issue**:
   - Read the relevant files before editing.
   - Apply the minimal change that addresses the issue. Do not refactor or clean up unrelated code.
   - After all fixes, run the project lint/test command (e.g., `cargo lint`, `cargo xtask test`) to verify nothing is broken.

5. **Commit and push**:
   - Follow the `commit-code` skill conventions: concise summary, `Signed-off-by`, and `Co-Authored-By`.
   - Use commit type `fix` for bug fixes, `test` for missing tests, `docs` for missing comments.
   - Push to the remote branch.

## Notes

- If a review issue was already addressed in a later commit on the same branch, skip it and note that it is resolved.
- If an issue requires a design decision (e.g., changing a public API), surface it to the user rather than making the call unilaterally.
- Env-var mutation in tests requires isolation (e.g., a `Mutex` guard) to prevent races when tests run in parallel.
