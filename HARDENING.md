<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--telegram-github-action/v1.4.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable tag (':v1') instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image if the tag is moved. The failing reference is: `image: 'docker://ghcr.io/cbrgm/telegram-github-action:v1'`. It should be pinned to a SHA digest, e.g. `image: 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>'`.

Locations:

- `action.yml:48`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag 'ghcr.io/cbrgm/telegram-github-action:v1' with the immutable SHA digest 'ghcr.io/cbrgm/telegram-github-action@sha256:ec8a3ae9a35f3d683b82cba7837a8cca8077631cf708197292c478279df3185c' in action.yml line 48. The original tag ':v1' is preserved as a comment for readability.

