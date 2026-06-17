<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.3.14

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--telegram-github-action/v1.3.14** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The Docker action's `image:` field references a mutable tag (`v1`) instead of an immutable SHA digest. This means the action could silently pull a different (potentially malicious) image if the tag is moved. The reference `docker://ghcr.io/cbrgm/telegram-github-action:v1` should be replaced with a pinned digest such as `docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:36`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag `ghcr.io/cbrgm/telegram-github-action:v1` with the immutable digest `ghcr.io/cbrgm/telegram-github-action@sha256:17c098d20332bd4db55d400f714be814a4017635a1cf236e647889e357306f3f # v1` in action.yml line 36.

