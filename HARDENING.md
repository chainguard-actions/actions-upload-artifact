<!-- markdownlint-disable -->

# Hardening Report: actions--upload-artifact/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--upload-artifact/v5.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All uses: references across workflow files use mutable version tags instead of pinned full-length SHA commit hashes, making the workflows vulnerable to supply-chain attacks. Failing references include: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4 (check-dist.yml); actions/checkout@v4, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3 (codeql-analysis.yml); actions/checkout@v4 (licensed.yml); actions/checkout@v4, actions/publish-immutable-action@0.0.3 (publish-immutable-actions.yml); actions/publish-action@v0.3.0 (release-new-action-version.yml); actions/checkout@v4, actions/setup-node@v4, multiple actions/download-artifact@v4 (test.yml). All should be pinned to a full 40-character hex commit SHA, e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.

Locations:

- `.github/workflows/check-dist.yml:22`
- `.github/workflows/check-dist.yml:25`
- `.github/workflows/check-dist.yml:43`
- `.github/workflows/codeql-analysis.yml:17`
- `.github/workflows/codeql-analysis.yml:22`
- `.github/workflows/codeql-analysis.yml:30`
- `.github/workflows/codeql-analysis.yml:42`
- `.github/workflows/licensed.yml:13`
- `.github/workflows/publish-immutable-actions.yml:14`
- `.github/workflows/publish-immutable-actions.yml:17`
- `.github/workflows/release-new-action-version.yml:21`
- `.github/workflows/test.yml:19`
- `.github/workflows/test.yml:22`

### missing-permissions (severity: medium)

Four workflow files have no top-level permissions: key and no job-level permissions: key on any job. Without explicit permissions, workflows inherit the default repository permissions which may be overly broad. check-dist.yml, codeql-analysis.yml, licensed.yml, and test.yml all lack any permissions declaration. Each should declare minimal required permissions such as permissions: contents: read.

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all uses: references to full 40-character SHA hashes with original tag preserved as comments. Added permissions blocks to check-dist.yml (contents: read), codeql-analysis.yml (contents: read + security-events: write for CodeQL), licensed.yml (contents: read), and test.yml (contents: read). The publish-immutable-actions.yml and release-new-action-version.yml already had permissions blocks and were only updated for unpinned action references. The actions/publish-immutable-action tag was resolved as v0.0.3 (the bare 0.0.3 tag was not found, but v0.0.3 resolved to SHA 4b1aa5c1cde5fedc80d52746c9546cb5560e5f53).

