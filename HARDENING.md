<!-- markdownlint-disable -->

# Hardening Report: cbrgm--telegram-github-action/v1.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cbrgm--telegram-github-action/v1.4.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable tag (`docker://ghcr.io/cbrgm/telegram-github-action:v1`) instead of an immutable SHA digest. This means the image could be replaced with a different (potentially malicious) version without changing the action definition, creating a supply-chain risk. It should be pinned to a specific SHA digest, e.g. `docker://ghcr.io/cbrgm/telegram-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from 'docker://ghcr.io/cbrgm/telegram-github-action:v1' to 'docker://ghcr.io/cbrgm/telegram-github-action@sha256:17c098d20332bd4db55d400f714be814a4017635a1cf236e647889e357306f3f' # v1. The SHA digest was resolved via the Docker Registry HTTP API v2.

