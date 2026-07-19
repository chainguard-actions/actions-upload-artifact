<!-- markdownlint-disable -->

# Hardening Report: actions--upload-artifact/v4.6.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--upload-artifact/v4.6.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag-based `uses:` references instead of pinned 40-character SHA commit hashes. This exposes the workflows to supply-chain attacks if any referenced action is compromised or its tag is moved. Affected references include: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4, actions/download-artifact@v4, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3, actions/publish-immutable-action@0.0.3, actions/publish-action@v0.3.0.

Locations:

- `.github/workflows/check-dist.yml:24`
- `.github/workflows/check-dist.yml:27`
- `.github/workflows/check-dist.yml:47`
- `.github/workflows/codeql-analysis.yml:18`
- `.github/workflows/codeql-analysis.yml:22`
- `.github/workflows/codeql-analysis.yml:30`
- `.github/workflows/codeql-analysis.yml:38`
- `.github/workflows/licensed.yml:16`
- `.github/workflows/publish-immutable-actions.yml:13`
- `.github/workflows/publish-immutable-actions.yml:15`
- `.github/workflows/release-new-action-version.yml:21`
- `.github/workflows/test.yml:23`
- `.github/workflows/test.yml:27`
- `.github/workflows/test.yml:60`
- `.github/workflows/test.yml:73`
- `.github/workflows/test.yml:85`
- `.github/workflows/test.yml:97`
- `.github/workflows/test.yml:148`
- `.github/workflows/test.yml:162`
- `.github/workflows/test.yml:176`
- `.github/workflows/test.yml:200`

### missing-permissions (severity: medium)

Four workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the default (potentially broad) token permissions, which may include write access to repository contents and other resources. The affected files are: check-dist.yml, codeql-analysis.yml, licensed.yml, and test.yml.

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references across 6 workflow files by resolving each tag to its full 40-character SHA (preserving the tag as a comment). Added top-level permissions blocks to the 4 workflow files that were missing them: check-dist.yml (contents: read), codeql-analysis.yml (contents: read + security-events: write for CodeQL), licensed.yml (contents: read), and test.yml (contents: read). The publish-immutable-actions.yml and release-new-action-version.yml already had permissions blocks and only needed action pinning. Note: actions/publish-immutable-action was referenced as @0.0.3 (without 'v' prefix) in the original; the correct tag is v0.0.3 which resolved to SHA 4b1aa5c1cde5fedc80d52746c9546cb5560e5f53.

