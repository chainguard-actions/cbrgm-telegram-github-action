<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.4.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image pinned to a mutable tag `:v1` instead of an immutable SHA digest. The reference `docker://ghcr.io/cbrgm/telegram-github-action:v1` can be silently replaced by a malicious image without any change to the action definition, enabling a supply-chain attack.

Locations:

- `action.yml:56`

### script-injection (severity: high)

Rule (a): Multiple `run:` steps in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` into shell commands. Because GitHub Actions performs YAML template substitution before the shell sees the string, a crafted PR URL containing shell metacharacters (`;`, `|`, `$(...)`, etc.) can execute arbitrary commands. Offending lines:
- Line 25: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`
- Line 30: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`
- Line 37: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`
- Line 42: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`
- Line 51: `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`
Fix: move the value into an `env:` variable and double-quote the shell expansion.

Locations:

- `.github/workflows/automerge.yml:25`
- `.github/workflows/automerge.yml:30`
- `.github/workflows/automerge.yml:37`
- `.github/workflows/automerge.yml:42`
- `.github/workflows/automerge.yml:51`

### script-injection (severity: high)

Rule (a): The 'Publish Git Tag' step in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into a `run:` shell script: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. A malicious or unexpected value from the step output containing shell metacharacters could lead to command injection. Fix: move the value into an `env:` variable and reference it as `"$NEW_TAG"` inside the script.

Locations:

- `.github/workflows/tag.yml:52`

### missing-permissions (severity: medium)

demo-workflow.yml has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be broader than necessary (e.g., `write` access to contents). A minimal `permissions:` block (e.g., `permissions: {}` or only the scopes actually needed) should be added.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed 4 findings: (1) Pinned docker://ghcr.io/cbrgm/telegram-github-action:v1 to immutable SHA digest sha256:ec8a3ae9a35f3d683b82cba7837a8cca8077631cf708197292c478279df3185c in action.yml. (2) Fixed 5 script injection instances in automerge.yml by moving github.event.pull_request.html_url into env: PR_URL and double-quoting the shell expansion. (3) Fixed script injection in tag.yml by moving steps.bump-semver.outputs.new_version into env: NEW_VERSION and referencing it as "$NEW_VERSION". (4) Added permissions: {} to demo-workflow.yml since the workflow only sends a Telegram message and requires no GitHub token permissions.

