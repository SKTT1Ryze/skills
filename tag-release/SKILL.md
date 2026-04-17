---
name: tag-release
description: Creates and pushes a Git tag to trigger the CD workflow for a versioned release. If no version is provided, prompts the user. Validates tag format, checks for existing tags, ensures the tag is created from main, and confirms before pushing.
---

When creating a release tag, follow these steps:

1. **Determine the version**: If the user provided a version (e.g. `v0.2.0`), use it directly. Otherwise, run `git tag --list 'v*' --sort=-version:refname | head -5` to show recent tags, then ask the user for the new version.

2. **Validate the tag format**: The tag must match `v[0-9]+.[0-9]+.[0-9]+` (e.g. `v1.2.3`). Reject anything that does not match and ask for a valid version.

3. **Ensure the current branch is main**: Run `git branch --show-current` and verify the result is `main`. If not, stop and tell the user that release tags must be created from the `main` branch.

4. **Check for conflicts**: Run `git tag -l <tag>` to confirm the tag does not already exist locally, and `git ls-remote --tags origin <tag>` to confirm it does not exist on the remote. If it exists, warn the user and stop.

5. **Confirm before acting**: Show the user the tag name and the commit it will point to (`git rev-parse --short HEAD`), then ask for confirmation before proceeding.

6. **Create and push the tag**:
   ```bash
   git tag <tag>
   git push origin <tag>
   ```

7. **Report the result**: Confirm the tag was pushed and remind the user that the CD workflow will now run on GitHub Actions to build and publish the release.

To delete a tag and re-push (if the user asks to redo or fix a tag):
```bash
git tag -d <tag>
git push origin :refs/tags/<tag>
```
Then repeat steps 5–7.
