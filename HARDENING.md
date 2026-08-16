<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.3.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.3.13** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple unpinned action/image references found:
- action.yml: `image: 'docker://ghcr.io/cbrgm/telegram-github-action:v1'` uses a mutable tag `:v1` instead of a SHA digest.
- demo-workflow.yml: `uses: cbrgm/telegram-github-action@main` uses a branch ref instead of a pinned SHA.
- stale.yml: `uses: cbrgm/cleanup-stale-branches-action@main` uses a branch ref instead of a pinned SHA.
- tag.yml: `uses: cbrgm/semver-bump-action@main` uses a branch ref instead of a pinned SHA.

Locations:

- `action.yml:40`
- `.github/workflows/demo-workflow.yml:10`
- `.github/workflows/stale.yml:36`
- `.github/workflows/tag.yml:47`

### script-injection (severity: high)

Direct interpolation of GitHub Actions expressions inside `run:` shell commands (sub-rule a):

automerge.yml: Five `run:` steps directly embed `${{ github.event.pull_request.html_url }}` into shell commands, e.g.:
  `run: gh pr review --approve ${{ github.event.pull_request.html_url }}`
  `run: gh pr merge --rebase --auto ${{ github.event.pull_request.html_url }}`
This allows an attacker to inject shell metacharacters via a crafted PR URL.

tag.yml: A `run:` block directly embeds a steps output expression:
  `new_tag=${{ steps.bump-semver.outputs.new_version }}`
This injects the step output value directly into the shell before quoting, enabling command injection.

Locations:

- `.github/workflows/automerge.yml:18`
- `.github/workflows/automerge.yml:23`
- `.github/workflows/automerge.yml:31`
- `.github/workflows/automerge.yml:36`
- `.github/workflows/automerge.yml:44`
- `.github/workflows/tag.yml:55`

### missing-permissions (severity: medium)

demo-workflow.yml has no top-level `permissions:` key and no job-level `permissions:` key on its only job (`notify`). Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad.

Locations:

- `.github/workflows/demo-workflow.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings: (1) Pinned all four unpinned action/image references to immutable SHAs/digests - docker image in action.yml pinned with sha256 digest preserving docker:// scheme and tag, three workflow action references pinned with full commit SHAs and comment tags. (2) Fixed all 5 script injection instances in automerge.yml by moving github.event.pull_request.html_url into env blocks as PR_URL, and fixed the tag.yml injection by moving steps.bump-semver.outputs.new_version into an env block as NEW_VERSION. (3) Added permissions: {} to demo-workflow.yml which had no permissions block.

