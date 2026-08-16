<!-- markdownlint-disable -->

# Hardening Report: bcanseco--github-contribution-graph-action/2.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bcanseco--github-contribution-graph-action/2.0.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference actions using mutable branch or tag refs instead of pinned full SHA digests, making them vulnerable to supply-chain attacks if the referenced branch or tag is updated maliciously.

Failing references:
- audit.yml: `actions/checkout@master`, `github/codeql-action/init@main`, `github/codeql-action/analyze@main`
- build.yml: `actions/checkout@master` (×3), `aevea/commitsar@master`, `gaurav-nelson/github-action-markdown-link-check@v1`, `./` (local, OK)
- release.yml: `actions-r-us/actions-tagger@latest`
- tests.yml: `actions/checkout@master`

All of these should be pinned to a full 40-character commit SHA (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/audit.yml:14`
- `.github/workflows/audit.yml:19`
- `.github/workflows/audit.yml:20`
- `.github/workflows/build.yml:13`
- `.github/workflows/build.yml:14`
- `.github/workflows/build.yml:18`
- `.github/workflows/build.yml:22`
- `.github/workflows/build.yml:26`
- `.github/workflows/release.yml:10`
- `.github/workflows/tests.yml:11`

### missing-permissions (severity: medium)

Multiple workflow files have no top-level `permissions:` key and no job-level `permissions:` blocks, meaning jobs run with the default (potentially broad) token permissions.

- `audit.yml`: No top-level or job-level permissions on either the `audit` or `codeql` jobs.
- `release.yml`: No top-level or job-level permissions on the `latest-major-version-tagger` job.
- `tests.yml`: No top-level or job-level permissions on the `tests` job.
- `build.yml`: The `commitizen` and `markdown` jobs have no permissions block (only `dogfooding` does).

Each workflow file should declare a top-level `permissions:` block with the minimum required scopes (e.g. `contents: read`), or every job should declare its own minimal permissions.

Locations:

- `.github/workflows/audit.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/tests.yml:1`
- `.github/workflows/build.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all mutable action refs to full SHA digests: actions/checkout@master→61b9e37, github/codeql-action/init@main→3434fbb, github/codeql-action/analyze@main→3434fbb, aevea/commitsar@master→df28a88, gaurav-nelson/github-action-markdown-link-check@v1→5c5dfc0, actions-r-us/actions-tagger@latest→330ddfa. Added top-level permissions blocks to all four workflow files: audit.yml (contents: read, security-events: write for CodeQL), build.yml (contents: read, with dogfooding job retaining its contents: write override), release.yml (contents: write for tag management), tests.yml (contents: read).

