<!-- markdownlint-disable -->

# Hardening Report: bcanseco--github-contribution-graph-action/v2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bcanseco--github-contribution-graph-action/v2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference actions using mutable branch/tag refs instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced action is compromised or altered.

Failing references:
- audit.yml: `actions/checkout@master`, `github/codeql-action/init@main`, `github/codeql-action/analyze@main`
- build.yml: `actions/checkout@master` (×2), `aevea/commitsar@master`, `gaurav-nelson/github-action-markdown-link-check@v1`
- release.yml: `actions-r-us/actions-tagger@latest`
- tests.yml: `actions/checkout@master`

Locations:

- `.github/workflows/audit.yml:12`
- `.github/workflows/audit.yml:15`
- `.github/workflows/audit.yml:16`
- `.github/workflows/build.yml:12`
- `.github/workflows/build.yml:13`
- `.github/workflows/build.yml:17`
- `.github/workflows/build.yml:18`
- `.github/workflows/build.yml:24`
- `.github/workflows/release.yml:9`
- `.github/workflows/tests.yml:11`

### missing-permissions (severity: medium)

Multiple workflow files have no top-level `permissions:` block and at least one job also lacks a job-level `permissions:` block, meaning jobs run with the default (overly broad) token permissions.

- `audit.yml`: No top-level permissions; neither the `audit` job nor the `codeql` job defines permissions.
- `tests.yml`: No top-level permissions; the `tests` job defines no permissions.
- `release.yml`: No top-level permissions; the `latest-major-version-tagger` job defines no permissions.
- `build.yml`: No top-level permissions; the `commitizen` and `markdown` jobs define no permissions (only `dogfooding` has a job-level `permissions:` block).

Locations:

- `.github/workflows/audit.yml:1`
- `.github/workflows/tests.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/build.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 4 workflow files:

**unpinned-uses**: Pinned all action references to full 40-char commit SHAs:
- actions/checkout@master → @61b9e3751b92087fd0b06925ba6dd6314e06f089 (used in audit.yml, build.yml, tests.yml)
- github/codeql-action/init@main → @d97b3428e8eebbb1810cf454d6397886d136b4ba (audit.yml)
- github/codeql-action/analyze@main → @d97b3428e8eebbb1810cf454d6397886d136b4ba (audit.yml)
- aevea/commitsar@master → @df28a88cf4ccd52bff867636af634f8b8df00d4a (build.yml)
- gaurav-nelson/github-action-markdown-link-check@v1 → @5c5dfc0ac2e225883c0e5f03a85311ec2830d368 (build.yml)
- actions-r-us/actions-tagger@latest → @330ddfac760021349fef7ff62b372f2f691c20fb (release.yml)

**missing-permissions**: Added `permissions: {}` at the top level of all 4 workflow files, and added job-level permissions blocks with minimum required permissions:
- audit/tests jobs: `contents: read`
- codeql job: `actions: read`, `contents: read`, `security-events: write`
- commitizen/markdown jobs: `contents: read`
- dogfooding/latest-major-version-tagger jobs: `contents: write` (need to push)

