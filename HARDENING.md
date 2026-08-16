<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.3.14

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.3.14** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image referenced by a mutable tag (`docker://ghcr.io/cbrgm/telegram-github-action:v1`) instead of an immutable SHA digest. This is vulnerable to supply-chain attacks if the tag is moved. It should be pinned to a SHA digest, e.g. `ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-digest>`.

Locations:

- `action.yml:36`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable branch refs (`@main`) instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks:
- `demo-workflow.yml`: `cbrgm/telegram-github-action@main`
- `stale.yml`: `cbrgm/cleanup-stale-branches-action@main`
- `tag.yml`: `cbrgm/semver-bump-action@main`

Locations:

- `.github/workflows/demo-workflow.yml:10`
- `.github/workflows/stale.yml:33`
- `.github/workflows/tag.yml:42`

### script-injection (severity: high)

Sub-rule (a): Five `run:` steps in automerge.yml directly interpolate `${{ github.event.pull_request.html_url }}` inside shell commands. This allows an attacker to inject arbitrary shell commands via a crafted pull request URL. The expression is substituted by the Actions runner before the shell parses the command, bypassing any shell quoting. Affected steps: 'Approve request' (dependabot job, line ~24), 'Enable automerge' (dependabot job, line ~27), 'Approve request' (renovate job, line ~37), 'Enable automerge' (renovate job, line ~40), 'Enable automerge' (cbrgm job, line ~49). Example offending line: `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`

Locations:

- `.github/workflows/automerge.yml:24`
- `.github/workflows/automerge.yml:27`
- `.github/workflows/automerge.yml:37`
- `.github/workflows/automerge.yml:40`
- `.github/workflows/automerge.yml:49`

### script-injection (severity: high)

Sub-rule (a) and (b): In tag.yml, the 'Publish Git Tag' run block directly interpolates `${{ steps.bump-semver.outputs.new_version }}` into a shell variable assignment: `new_tag=${{ steps.bump-semver.outputs.new_version }}`. The expression is substituted before the shell parses the script, enabling command injection. Additionally, the resulting `$new_tag` variable is used unquoted in `git tag $new_tag` and `git push origin $new_tag`, compounding the risk.

Locations:

- `.github/workflows/tag.yml:52`

### missing-permissions (severity: medium)

demo-workflow.yml has no top-level `permissions:` key and its only job (`notify`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions (which may include broad write access), violating the principle of least privilege. A `permissions: {}` or specific minimal permissions block should be added.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 5 findings: (1) Pinned Docker image in action.yml to sha256 digest while preserving docker:// scheme and :v1 tag. (2) Pinned cbrgm/telegram-github-action@main to full SHA in demo-workflow.yml and added top-level permissions: {}. (3) Pinned cbrgm/cleanup-stale-branches-action@main to full SHA in stale.yml. (4) Pinned cbrgm/semver-bump-action@main to full SHA in tag.yml and fixed script injection by moving new_version output into env var NEW_VERSION with proper quoting. (5) Fixed all 5 script injection instances in automerge.yml by moving github.event.pull_request.html_url into env var PR_URL and using double-quoted "$PR_URL" in shell commands.

