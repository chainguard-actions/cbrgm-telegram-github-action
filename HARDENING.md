<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.4.3** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml references the Docker image 'docker://ghcr.io/cbrgm/telegram-github-action:v1' using a mutable tag (v1) instead of an immutable SHA digest. This means the image can be silently replaced with a different (potentially malicious) version without any change to the action definition. The image reference should use a SHA digest, e.g. 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:56`

### script-injection (severity: high)

automerge.yml directly interpolates ${{ github.event.pull_request.html_url }} inside run: shell commands (rule a — direct expression interpolation). Although this is a GitHub-controlled field, any ${{ ... }} expression interpolated directly into a shell command is a script-injection risk because the value is substituted into the shell string before the shell parses it. An attacker who can influence the PR URL (e.g. via a crafted repository name or redirect) could inject shell metacharacters. The fix is to pass the URL via an env: variable and reference it as a quoted shell variable: env: PR_URL: ${{ github.event.pull_request.html_url }} / run: gh pr review --approve "$PR_URL". Affected steps: 'Approve request' (dependabot job, line 24), 'Enable automerge' (dependabot job, line 30), 'Approve request' (renovate job, line 37), 'Enable automerge' (renovate job, line 43), 'Enable automerge' (cbrgm job, line 51).

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:30`
- `.github/workflows/automerge.yml:37`
- `.github/workflows/automerge.yml:43`
- `.github/workflows/automerge.yml:51`

### script-injection (severity: high)

tag.yml directly interpolates ${{ steps.bump-semver.outputs.new_version }} inside a run: shell command (rule a — direct expression interpolation). The step output value is substituted into the shell string before the shell parses it, allowing shell metacharacters in the output to be interpreted. The fix is to pass the value via an env: variable and reference it as a quoted shell variable: env: NEW_TAG: ${{ steps.bump-semver.outputs.new_version }} / run: new_tag="$NEW_TAG". Affected step: 'Publish Git Tag' (line 55, `new_tag=${{ steps.bump-semver.outputs.new_version }}`).

Locations:

- `.github/workflows/tag.yml:55`

### missing-permissions (severity: medium)

demo-workflow.yml has no top-level permissions: key and no job-level permissions: key on its single job ('notify'). Without an explicit permissions block, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. write access to contents). A minimal permissions block such as 'permissions: {}' or 'permissions: contents: read' should be added.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed 4 findings across 4 files: (1) action.yml: Pinned Docker image 'docker://ghcr.io/cbrgm/telegram-github-action:v1' to immutable SHA digest 'sha256:2e444b4f7aec16c6a4cc15573f2c9beb47100cf8a289dadd5b14c664eeb8069c'. (2) automerge.yml: Moved all 5 occurrences of ${{ github.event.pull_request.html_url }} from run: shell strings into env: blocks as PR_URL, referenced as "$PR_URL" in shell commands. (3) tag.yml: Moved ${{ steps.bump-semver.outputs.new_version }} from the run: shell string into an env: block as NEW_VERSION, referenced as "$NEW_VERSION" in the shell script. (4) demo-workflow.yml: Added top-level 'permissions: {}' block to prevent inheriting overly broad default token permissions.

