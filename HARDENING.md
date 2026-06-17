<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.3.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--telegram-github-action/v1.3.13** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The Docker action image reference in action.yml uses a mutable tag (':v1') instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image if the tag is moved. The reference 'docker://ghcr.io/cbrgm/telegram-github-action:v1' should be pinned to a specific SHA digest, e.g. 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>'.

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag 'docker://ghcr.io/cbrgm/telegram-github-action:v1' with the immutable SHA256 digest 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:17c098d20332bd4db55d400f714be814a4017635a1cf236e647889e357306f3f' in action.yml line 44. The original tag ':v1' is preserved as a comment outside the YAML quotes for readability.

