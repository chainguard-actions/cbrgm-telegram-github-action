<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.4.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image with a mutable tag instead of a SHA digest. `image: 'docker://ghcr.io/cbrgm/telegram-github-action:v1'` references the `:v1` tag, which can be silently replaced by a malicious image. It should be pinned to a SHA digest, e.g. `docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:57`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` into shell commands. The expression is substituted by the Actions runner before the shell parses the command, allowing an attacker to craft a PR URL containing shell metacharacters to achieve command injection. Offending lines:
- `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (dependabot job, line 24)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (dependabot job, line 30)
- `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (renovate job, line 38)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (renovate job, line 44)
- `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (cbrgm job, line 51)
Fix: move the URL into an env var and reference it as a quoted shell variable, e.g. `env: PR_URL: ${{ github.event.pull_request.html_url }}` then `run: gh pr review --approve "$PR_URL"`.

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:30`
- `.github/workflows/automerge.yml:38`
- `.github/workflows/automerge.yml:44`
- `.github/workflows/automerge.yml:51`

### script-injection (severity: high)

Sub-rule (a): The 'Publish Git Tag' step in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` inside a `run:` shell script: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. Any expression inside a run block is substituted by the Actions runner before the shell parses it, so a malicious value in `new_version` could inject arbitrary shell commands. Fix: move the value into an env var and reference it as a quoted shell variable, e.g. `env: NEW_TAG: ${{ steps.bump-semver.outputs.new_version }}` then `new_tag="$NEW_TAG"`.

Locations:

- `.github/workflows/tag.yml:55`

### missing-permissions (severity: medium)

The workflow file demo-workflow.yml has no top-level `permissions:` key and the single job `notify` also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal permissions block (e.g. `permissions: {}` or `permissions: contents: read`) should be added.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

1. action.yml: Pinned Docker image from 'docker://ghcr.io/cbrgm/telegram-github-action:v1' to 'docker://ghcr.io/cbrgm/telegram-github-action:v1@sha256:ec8a3ae9a35f3d683b82cba7837a8cca8077631cf708197292c478279df3185c'. 2. automerge.yml: Fixed all 5 script injection instances by moving ${{ github.event.pull_request.html_url }} into env vars (PR_URL) and referencing as "$PR_URL" in run blocks. 3. tag.yml: Fixed script injection by moving ${{ steps.bump-semver.outputs.new_version }} into env var (NEW_VERSION) and referencing as "$NEW_VERSION" in the run block. 4. demo-workflow.yml: Added top-level 'permissions: {}' block to restrict the default token permissions.

