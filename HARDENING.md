<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.3.15

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.3.15** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml docker image reference uses a mutable tag (:v1) instead of a SHA digest. This means the image can be silently replaced with a different version, enabling supply-chain attacks. The reference `docker://ghcr.io/cbrgm/telegram-github-action:v1` should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:44`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` into shell commands. This value is attacker-controlled (a PR submitter can craft a malicious URL containing shell metacharacters). Affected steps: 'Approve request' (dependabot job, line 24), 'Enable automerge' (dependabot job, line 29), 'Approve request' (renovate job, line 36), 'Enable automerge' (renovate job, line 41), 'Enable automerge' (cbrgm job, line 48). The value should be passed via an env var and double-quoted in the shell command instead.

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:29`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:41`
- `.github/workflows/automerge.yml:48`

### script-injection (severity: high)

Sub-rule (a): The 'Publish Git Tag' `run:` block in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into a shell variable assignment: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. Step outputs flow through YAML template substitution before the shell sees them, so a malicious value could inject shell metacharacters. The value should be passed via an `env:` variable and double-quoted in the script.

Locations:

- `.github/workflows/tag.yml:57`

### permissions (severity: medium)

missing-permissions: demo-workflow.yml has no top-level `permissions:` key and its only job ('notify') also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal `permissions: {}` or specific scopes should be declared.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, permissions

**Notes:**

1. action.yml: Pinned docker image from 'docker://ghcr.io/cbrgm/telegram-github-action:v1' to 'docker://ghcr.io/cbrgm/telegram-github-action:v1@sha256:ec8a3ae9a35f3d683b82cba7837a8cca8077631cf708197292c478279df3185c' (preserving tag and docker:// scheme). 2. automerge.yml: Fixed all 5 script injection instances by moving ${{ github.event.pull_request.html_url }} into an env var PR_URL and double-quoting it in each run: command (dependabot Approve, dependabot Enable automerge, renovate Approve, renovate Enable automerge, cbrgm Enable automerge). 3. tag.yml: Fixed script injection in 'Publish Git Tag' step by moving ${{ steps.bump-semver.outputs.new_version }} into env var NEW_VERSION and double-quoting it in the shell script. 4. demo-workflow.yml: Added top-level 'permissions: {}' to prevent inheriting overly broad default token permissions.

