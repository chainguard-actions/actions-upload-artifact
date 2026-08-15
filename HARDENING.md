<!-- markdownlint-disable -->

# Hardening Report: actions--upload-artifact/v7.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **actions--upload-artifact/v7.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): `${{ matrix.runs-on }}` is interpolated directly inside `run:` shell command strings in two steps of the 'build' job. In the 'Create direct upload file' step: `run: echo -n 'direct file upload content' > direct-upload-${{ matrix.runs-on }}.txt`. In the 'Verify direct file artifact' step: `$file = "direct-download/direct-upload-${{ matrix.runs-on }}.txt"`. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, allowing metacharacter injection.

Locations:

- `.github/workflows/test.yml:176`
- `.github/workflows/test.yml:186`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or branch names instead of immutable 40-character commit SHAs, making them vulnerable to supply-chain attacks.

- test.yml: `actions/checkout@v4`, `actions/setup-node@v4`, `actions/download-artifact@main` (branch ref), `actions/github-script@v8`
- check-dist.yml: `actions/checkout@v4`, `actions/setup-node@v4`, `actions/upload-artifact@v4`
- codeql-analysis.yml: `actions/checkout@v4`, `github/codeql-action/init@v3`, `github/codeql-action/autobuild@v3`, `github/codeql-action/analyze@v3`
- licensed.yml: `actions/checkout@v4`
- publish-immutable-actions.yml: `actions/checkout@v4`, `actions/publish-immutable-action@0.0.3`
- release-new-action-version.yml: `actions/publish-action@v0.3.0`
- test-proxy.yml: `actions/checkout@v4`

Locations:

- `.github/workflows/test.yml:30`
- `.github/workflows/check-dist.yml:22`
- `.github/workflows/codeql-analysis.yml:16`
- `.github/workflows/licensed.yml:10`
- `.github/workflows/publish-immutable-actions.yml:14`
- `.github/workflows/release-new-action-version.yml:18`
- `.github/workflows/test-proxy.yml:47`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions, violating the principle of least privilege.

- licensed.yml: no permissions block at top level or job level
- codeql-analysis.yml: no permissions block at top level or job level
- check-dist.yml: no permissions block at top level or job level

Locations:

- `.github/workflows/licensed.yml:1`
- `.github/workflows/codeql-analysis.yml:1`
- `.github/workflows/check-dist.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:

1. script-injection (test.yml lines 176, 186): Moved `${{ matrix.runs-on }}` out of two `run:` blocks into `env:` blocks as `RUNS_ON`, then referenced as `${RUNS_ON}` in bash and `$env:RUNS_ON` in PowerShell.

2. unpinned-uses: Pinned all mutable action references to full commit SHAs:
   - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
   - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
   - actions/download-artifact@main → @484a0b528fb4d7bd804637ccb632e47a0e638317
   - actions/github-script@v8 → @ed597411d8f924073f98dfc5c65a23a2325f34cd
   - actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02
   - github/codeql-action/init@v3 → @4187e74d05793876e9989daffde9c3e66b4acd07
   - github/codeql-action/autobuild@v3 → @4187e74d05793876e9989daffde9c3e66b4acd07
   - github/codeql-action/analyze@v3 → @4187e74d05793876e9989daffde9c3e66b4acd07
   - actions/publish-immutable-action@0.0.3 → @4b1aa5c1cde5fedc80d52746c9546cb5560e5f53 (resolved as v0.0.3)
   - actions/publish-action@v0.3.0 → @f784495ce78a41bac4ed7e34a73f0034015764bb

3. missing-permissions: Added `permissions:` blocks to check-dist.yml (contents: read), codeql-analysis.yml (contents: read + security-events: write for CodeQL), and licensed.yml (contents: read).

