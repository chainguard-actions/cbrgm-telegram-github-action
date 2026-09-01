<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.4.4** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a mutable Docker image tag `:v1` instead of a SHA digest for the `runs.image:` field. The reference `docker://ghcr.io/cbrgm/telegram-github-action:v1` can be silently replaced by a malicious image without any change to the action definition, enabling a supply-chain attack.

Locations:

- `action.yml:57`

### script-injection (severity: high)

Sub-rule (a): Five `run:` steps in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` into shell commands. An attacker can craft a pull request whose URL contains shell metacharacters or commands that will be executed by the runner. Offending lines: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}` (dependabot/approve, renovate/approve) and `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}` (dependabot/automerge, renovate/automerge, cbrgm/automerge). These should be moved to an `env:` variable and the variable double-quoted in the shell command.

Locations:

- `.github/workflows/automerge.yml:23`
- `.github/workflows/automerge.yml:28`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:41`
- `.github/workflows/automerge.yml:49`

### script-injection (severity: high)

Sub-rule (a): The 'Publish Git Tag' step in tag.yml directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into a shell script: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The step output value is substituted into the shell command string before the shell parses it, allowing injection of arbitrary shell commands if the upstream action produces a malicious output. The value should be passed via an `env:` variable and double-quoted in the script.

Locations:

- `.github/workflows/tag.yml:52`

### missing-permissions (severity: medium)

The workflow file demo-workflow.yml has no top-level `permissions:` key and the single job `notify` also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal `permissions: {}` or specific scopes (e.g. `contents: read`) should be added.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed 4 findings: (1) Pinned docker://ghcr.io/cbrgm/telegram-github-action:v1 to its SHA digest sha256:e060b568748102a147f43f11a5f2f44801ced14b61c0e8b3e80a06a3b409f8e6 in action.yml, preserving the docker:// scheme and :v1 tag. (2) Fixed 5 script injection instances in automerge.yml by moving github.event.pull_request.html_url into env: blocks as PR_URL and double-quoting in shell commands. (3) Fixed script injection in tag.yml by moving steps.bump-semver.outputs.new_version into an env: block as NEW_VERSION and double-quoting in the shell script. (4) Added permissions: {} to demo-workflow.yml to prevent inheriting overly broad default token permissions.

