<!-- markdownlint-disable -->

# Hardening Report: bufbuild--buf-lint-action/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bufbuild--buf-lint-action/v1.1.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions and reusable workflows using mutable tags or branch names instead of pinned full-length SHA commit hashes. This exposes the workflow to supply-chain attacks if the referenced tag or branch is updated with malicious code.

Failing references:
- ci.yaml: `actions/checkout@v4`, `actions/setup-go@v5`, `actions/setup-node@v4`, `bufbuild/buf-setup-action@v1`
- add-to-project.yaml: `bufbuild/base-workflows/.github/workflows/add-to-project.yaml@main`
- emergency-review-bypass.yaml: `bufbuild/base-workflows/.github/workflows/emergency-review-bypass.yaml@main`
- notify-approval-bypass.yaml: `bufbuild/base-workflows/.github/workflows/notify-approval-bypass.yaml@main`
- pr-title.yaml: `bufbuild/base-workflows/.github/workflows/pr-title.yaml@main`

Locations:

- `.github/workflows/ci.yaml:15`
- `.github/workflows/ci.yaml:19`
- `.github/workflows/ci.yaml:23`
- `.github/workflows/ci.yaml:29`
- `.github/workflows/add-to-project.yaml:17`
- `.github/workflows/emergency-review-bypass.yaml:10`
- `.github/workflows/notify-approval-bypass.yaml:9`
- `.github/workflows/pr-title.yaml:17`

### broad-permissions (severity: medium)

ci.yaml sets top-level `permissions: read-all`, which grants read access to all repository scopes. This is overly broad and should be replaced with specific minimal permissions (e.g., `contents: read`) scoped only to what each job actually requires.

Locations:

- `.github/workflows/ci.yaml:10`

### missing-permissions (severity: medium)

add-to-project.yaml has no top-level `permissions:` key and no job-level `permissions:` key on its only job (`call-workflow-add-to-project`). Without explicit permissions, the workflow inherits the default token permissions, which may be broader than necessary. A minimal explicit permissions block (e.g., `permissions: {}` or specific scopes) should be added.

Locations:

- `.github/workflows/add-to-project.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, broad-permissions, missing-permissions

**Notes:**

Fixed all findings across 5 workflow files:

1. unpinned-uses: Pinned all 8 mutable references to full commit SHAs:
   - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
   - actions/setup-go@v5 → @40f1582b2485089dde7abd97c1529aa768e1baff
   - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
   - bufbuild/buf-setup-action@v1 → @a47c93e0b1648d5651a065437926377d060baa99
   - bufbuild/base-workflows@main (4 files) → @7c0b54b718244c78f00037343ff7cb33ef7caca9

2. broad-permissions: Replaced `permissions: read-all` in ci.yaml with `permissions: contents: read` (minimal scope needed for checkout and build).

3. missing-permissions: Added `permissions: {}` to add-to-project.yaml since the workflow itself needs no direct permissions (it delegates to a reusable workflow via secrets: inherit).

