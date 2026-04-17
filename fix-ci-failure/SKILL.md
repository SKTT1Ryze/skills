---
name: fix-ci-failure
description: Fetches the open PR for the current branch, extracts CI failure logs, fixes all reported errors, and pushes the changes back to the remote repository.
---

Read CI failure logs for the current branch's pull request and fix all reported errors.

## Key principles

1. **Wait for CI to finish**: Do not act on pending checks. Confirm all jobs have a terminal status (pass/fail) before reading logs.
2. **Fix root causes**: Diagnose why the failure happened before editing files. Do not blindly patch error messages.
3. **Verify before pushing**: Re-run the equivalent local command (e.g., `cargo lint`, `cargo xtask test`) after fixes to confirm the error is resolved.

## Execution steps (in order)

1. **Find the PR for the current branch**:
   - Run `gh pr list --head $(git branch --show-current) --json number,title,url` to find the open PR.
   - If no PR exists, inform the user and stop.

2. **Check CI status**:
   - Run `gh pr checks <number> --repo <owner/repo>` to list all checks and their status.
   - If any checks are still pending, inform the user and wait or stop.
   - If all checks pass, inform the user and stop — nothing to fix.

3. **Fetch failure logs**:
   - For each failing check, extract the run ID from the check URL.
   - Run `gh run view <run-id> --log-failed` to retrieve only the failed step logs.
   - If the output is too large, use `gh run view <run-id> --log-failed | head -200` to get the most relevant part.

4. **Diagnose and summarize errors**:
   - Parse the log output to identify the failing job, step name, and error message.
   - Map errors to files and line numbers where possible (e.g., compiler errors, lint violations, test failures).
   - Present a clear summary to the user before making changes.

5. **Fix each error**:
   - Read the relevant files before editing.
   - Apply the minimal change that resolves the error. Do not refactor or clean up unrelated code.
   - After all fixes, run the local equivalent of the failing CI step to verify the fix (e.g., `cargo lint` for lint failures, `cargo xtask test` for test failures, `cargo +nightly fmt -- --check` for format failures).

6. **Commit and push**:
   - Follow the `commit-code` skill conventions: concise summary, `Signed-off-by`, and `Co-Authored-By`.
   - Use commit type `fix` for build/test errors, `style` for formatting failures, `ci` for CI config issues.
   - Push to the remote branch.

## Notes

- A single CI run may contain multiple failing jobs (e.g., `ubuntu-latest` and `macos-latest`). Check all of them — the root cause is often the same but sometimes differs by platform.
- If a failure is flaky (network timeout, cache miss, unrelated infrastructure error), inform the user rather than attempting a code fix.
- If fixing the error requires a design decision, surface it to the user rather than making the call unilaterally.
