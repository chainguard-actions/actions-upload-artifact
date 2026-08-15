<!-- markdownlint-disable -->

# Hardening Report: actions--upload-artifact/v7.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--upload-artifact/v7.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of pinned full-length SHA digests, making them vulnerable to supply-chain attacks if the referenced tag is moved or the branch is updated.

check-dist.yml: actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4
codeql-analysis.yml: actions/checkout@v4, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3
licensed.yml: actions/checkout@v4
publish-immutable-actions.yml: actions/checkout@v4, actions/publish-immutable-action@0.0.3
release-new-action-version.yml: actions/publish-action@v0.3.0
test-proxy.yml: actions/checkout@v4
test.yml: actions/checkout@v4, actions/setup-node@v4, actions/download-artifact@main (multiple), actions/github-script@v8

Locations:

- `.github/workflows/check-dist.yml:24`
- `.github/workflows/check-dist.yml:27`
- `.github/workflows/check-dist.yml:44`
- `.github/workflows/codeql-analysis.yml:19`
- `.github/workflows/codeql-analysis.yml:23`
- `.github/workflows/codeql-analysis.yml:30`
- `.github/workflows/codeql-analysis.yml:40`
- `.github/workflows/licensed.yml:14`
- `.github/workflows/publish-immutable-actions.yml:13`
- `.github/workflows/publish-immutable-actions.yml:16`
- `.github/workflows/release-new-action-version.yml:21`
- `.github/workflows/test-proxy.yml:38`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:29`
- `.github/workflows/test.yml:87`
- `.github/workflows/test.yml:155`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` key and no job-level `permissions:` keys on any of their jobs. Without explicit permissions, workflows inherit the default repository permissions (which may be broad), violating the principle of least privilege.

- check-dist.yml: no permissions block at all
- codeql-analysis.yml: no permissions block at all
- licensed.yml: no permissions block at all

Locations:

- `.github/workflows/check-dist.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/licensed.yml:1`

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are interpolated directly inside `run:` shell command strings in test.yml. The value `${{ matrix.runs-on }}` is controlled by the workflow matrix and flows through YAML template substitution before the shell processes it, allowing shell metacharacter injection.

Offending lines:
1. `run: echo -n 'direct file upload content' > direct-upload-${{ matrix.runs-on }}.txt` — bash run block with matrix expression directly in shell command
2. `$file = "direct-download/direct-upload-${{ matrix.runs-on }}.txt"` — PowerShell run block with matrix expression directly interpolated in shell string

Locations:

- `.github/workflows/test.yml:131`
- `.github/workflows/test.yml:141`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all findings across 7 workflow files:

**unpinned-uses**: Pinned all action references to full SHA digests:
- actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5
- actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
- actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02
- github/codeql-action/init@v3 → @b7351df727350dca84cb9d725d57dcf5bc82ba26
- github/codeql-action/autobuild@v3 → @b7351df727350dca84cb9d725d57dcf5bc82ba26
- github/codeql-action/analyze@v3 → @b7351df727350dca84cb9d725d57dcf5bc82ba26
- actions/publish-immutable-action@0.0.3 → @4b1aa5c1cde5fedc80d52746c9546cb5560e5f53 (resolved as v0.0.3)
- actions/publish-action@v0.3.0 → @f784495ce78a41bac4ed7e34a73f0034015764bb
- actions/download-artifact@main → @484a0b528fb4d7bd804637ccb632e47a0e638317 (6 occurrences in test.yml)
- actions/github-script@v8 → @ed597411d8f924073f98dfc5c65a23a2325f34cd

**missing-permissions**: Added top-level permissions blocks to:
- check-dist.yml: `contents: read`
- codeql-analysis.yml: `contents: read` + `security-events: write` (required for CodeQL)
- licensed.yml: `contents: read`

**script-injection**: Fixed two run: blocks in test.yml that interpolated `${{ matrix.runs-on }}` directly in shell strings:
- Bash step 'Create direct upload file': moved expression to env var RUNS_ON, used `${RUNS_ON}` in shell
- PowerShell step 'Verify direct file artifact': moved expression to env var RUNS_ON, used `${env:RUNS_ON}` in PowerShell

