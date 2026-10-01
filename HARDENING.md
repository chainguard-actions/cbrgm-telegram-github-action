<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cbrgm--telegram-github-action/v1.4.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image referenced by a mutable tag rather than an immutable SHA digest. The image 'docker://ghcr.io/cbrgm/telegram-github-action:v1' uses the tag ':v1', which can be silently replaced with a malicious image at any time. This exposes all users of this action to supply-chain attacks. The image reference should be pinned to a full SHA256 digest, e.g. 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest> # v1'.

Locations:

- `action.yml:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in hardened/action/action.yml from 'docker://ghcr.io/cbrgm/telegram-github-action:v1' to 'docker://ghcr.io/cbrgm/telegram-github-action:v1@sha256:d410dfccca788758f89f4fe9fa52eb5ca49a5622c63c1248184c16c33846e016'. The docker:// scheme and :v1 tag are preserved; the SHA256 digest makes the reference immutable and resistant to supply-chain attacks.

