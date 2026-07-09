<!-- markdownlint-disable -->

# Hardening Report: actions--upload-artifact/v4.6.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **actions--upload-artifact/v4.6.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags (e.g., @v4, @v3, @v3.3.0, @0.0.3) instead of immutable full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the tag is moved to a different commit. Affected references include: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4, actions/download-artifact@v4, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3, actions/publish-immutable-action@0.0.3, actions/publish-action@v0.3.0.

Locations:

- `.github/workflows/check-dist.yml:21`
- `.github/workflows/check-dist.yml:26`
- `.github/workflows/check-dist.yml:43`
- `.github/workflows/codeql-analysis.yml:16`
- `.github/workflows/codeql-analysis.yml:21`
- `.github/workflows/codeql-analysis.yml:27`
- `.github/workflows/codeql-analysis.yml:33`
- `.github/workflows/licensed.yml:13`
- `.github/workflows/publish-immutable-actions.yml:14`
- `.github/workflows/publish-immutable-actions.yml:17`
- `.github/workflows/release-new-action-version.yml:22`
- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:57`
- `.github/workflows/test.yml:68`
- `.github/workflows/test.yml:82`
- `.github/workflows/test.yml:96`
- `.github/workflows/test.yml:131`
- `.github/workflows/test.yml:143`
- `.github/workflows/test.yml:163`
- `.github/workflows/test.yml:193`

### missing-permissions (severity: medium)

Four workflow files have no top-level 'permissions:' key and no job-level 'permissions:' key on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions. Each of these files should declare minimal required permissions.

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by replacing mutable tags with full 40-character SHA commit hashes (with tag comments for readability). Added top-level 'permissions: contents: read' to check-dist.yml, licensed.yml, and test.yml. Added 'permissions: contents: read\n  security-events: write' to codeql-analysis.yml (security-events: write is required for CodeQL to upload SARIF results). Pinned: actions/checkout@v4 -> 34e114876b0b11c390a56381ad16ebd13914f8d5, actions/setup-node@v4 -> 49933ea5288caeca8642d1e84afbd3f7d6820020, actions/upload-artifact@v4 -> ea165f8d65b6e75b540449e92b4886f43607fa02, actions/download-artifact@v4 -> d3f86a106a0bac45b974a628896c90dbdf5c8093, github/codeql-action/{init,autobuild,analyze}@v3 -> 02c5e83432fe5497fd85b873b6c9f16a8578e1d9, actions/publish-immutable-action@0.0.3 -> 4b1aa5c1cde5fedc80d52746c9546cb5560e5f53 (resolved as v0.0.3), actions/publish-action@v0.3.0 -> f784495ce78a41bac4ed7e34a73f0034015764bb.

