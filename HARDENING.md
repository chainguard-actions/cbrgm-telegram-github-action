<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.4.2** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml runs.image: field references a mutable Docker image tag ('docker://ghcr.io/cbrgm/telegram-github-action:v1') instead of an immutable SHA digest. A tag can be silently overwritten to point to a different (potentially malicious) image, making this a supply-chain risk. It should be pinned to a SHA digest, e.g. 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:56`

### script-injection (severity: high)

Sub-rule (a): Multiple run: blocks in automerge.yml directly interpolate the GitHub Actions expression ${{ github.event.pull_request.html_url }} into shell commands. This value is attacker-controlled (a PR author can craft a URL with shell metacharacters) and is substituted into the shell command string before the shell parses it, enabling command injection. Affected steps: 'Approve request' (dependabot job, line ~24), 'Enable automerge' (dependabot job, line ~29), 'Approve request' (renovate job, line ~38), 'Enable automerge' (renovate job, line ~43), 'Enable automerge' (cbrgm job, line ~51). Example offending line: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:29`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:43`
- `.github/workflows/automerge.yml:51`

### script-injection (severity: high)

Sub-rule (a): The 'Publish Git Tag' run: block in tag.yml directly interpolates ${{ steps.bump-semver.outputs.new_version }} into a shell command: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The step output is a workflow-controllable value (derived from workflow_dispatch inputs bump-level and prerelease-tag) and is substituted into the shell string before the shell parses it, enabling command injection via crafted version strings.

Locations:

- `.github/workflows/tag.yml:57`

### missing-permissions (severity: medium)

The workflow file demo-workflow.yml has no top-level permissions: key and no job-level permissions: key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal permissions block (e.g. permissions: {} or only the specific scopes needed) should be added.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

1. action.yml: Pinned 'docker://ghcr.io/cbrgm/telegram-github-action:v1' to 'docker://ghcr.io/cbrgm/telegram-github-action:v1@sha256:ec8a3ae9a35f3d683b82cba7837a8cca8077631cf708197292c478279df3185c' to prevent supply-chain attacks via mutable tags.
2. automerge.yml: Fixed 5 script-injection instances by moving ${{ github.event.pull_request.html_url }} into env: blocks as PR_URL and referencing "$PR_URL" (double-quoted) in the shell run: commands.
3. tag.yml: Fixed script-injection by moving ${{ steps.bump-semver.outputs.new_version }} into an env: block as NEW_VERSION and referencing "$NEW_VERSION" in the shell script.
4. demo-workflow.yml: Added 'permissions: {}' top-level block to prevent inheriting overly broad default token permissions.

