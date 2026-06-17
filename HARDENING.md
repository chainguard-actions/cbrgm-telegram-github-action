<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--telegram-github-action/v1.4.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action's Docker image reference uses a mutable tag `:v1` instead of an immutable SHA digest. This means the image pulled at runtime could change without notice, enabling supply-chain attacks. The failing reference is: `image: 'docker://ghcr.io/cbrgm/telegram-github-action:v1'`. It should be replaced with a pinned SHA digest, e.g. `image: 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag ':v1' with the immutable SHA256 digest in action.yml line 52. The image reference is now: 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:17c098d20332bd4db55d400f714be814a4017635a1cf236e647889e357306f3f' # v1

