<!-- markdownlint-disable -->

# Hardening Report: michidk--run-komac/v2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **michidk--run-komac/v2** was hardened automatically. 13 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple ${{ inputs.* }} expressions are interpolated directly inside run: shell commands in action.yml, violating sub-rule (a). (1) 'Install Komac' step: `${{ inputs.komac-version }}` appears both inside a quoted string comparison and unquoted in `cargo binstall komac@${{ inputs.komac-version }}`. (2) 'Set Custom Environment Variables' step: `${{ inputs.custom-fork-owner }}`, `${{ inputs.custom-tool }}`, and `${{ inputs.custom-tool-url }}` are interpolated directly in the run: block. (3) 'Run Komac' step: `komac ${{ inputs.args }}` passes user-controlled input directly to the shell — an attacker can inject arbitrary shell commands via the `args` input.

Locations:

- `action.yml:43`
- `action.yml:45`
- `action.yml:51`
- `action.yml:54`
- `action.yml:57`
- `action.yml:62`

### github-env-injection (severity: high)

The 'Set Custom Environment Variables' step in action.yml writes user-controlled inputs directly to $GITHUB_ENV without sanitization. Specifically, `echo "KOMAC_FORK_OWNER=${{ inputs.custom-fork-owner }}" >> $GITHUB_ENV`, `echo "KOMAC_CREATED_WITH=${{ inputs.custom-tool }}" >> $GITHUB_ENV`, and `echo "KOMAC_CREATED_WITH_URL=${{ inputs.custom-tool-url }}" >> $GITHUB_ENV` all write unsanitized input values. An attacker can inject newlines into these values to set arbitrary environment variables (e.g., override PATH or inject new KEY=VALUE pairs). The required sanitization step `printf '%s' "$VAR" | tr -d '\n\r'` is absent.

Locations:

- `action.yml:51`
- `action.yml:54`
- `action.yml:57`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable tags or branch names instead of immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the referenced tag or branch is moved or compromised. Failing references: action.yml: `cargo-bins/cargo-binstall@main` (branch); .github/workflows/pr-stale.yml: `actions/stale@v11` (tag); .github/workflows/pr-title.yml: `aslafy-z/conventional-pr-title-action@v3` (tag); .github/workflows/test.yml: `actions/checkout@v7` (tag); .github/workflows/versioning.yml: `Actions-R-Us/actions-tagger@latest` (branch).

Locations:

- `action.yml:36`
- `.github/workflows/pr-stale.yml:17`
- `.github/workflows/pr-title.yml:11`
- `.github/workflows/test.yml:14`
- `.github/workflows/versioning.yml:10`

### missing-permissions (severity: medium)

Two workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs, meaning they run with the default (potentially broad) token permissions. `test.yml` (triggered on push) and `versioning.yml` (triggered on release) both lack any permissions restriction.

Locations:

- `.github/workflows/test.yml:1`
- `.github/workflows/versioning.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.komac-version }}" appears directly in run: block of step "Install Komac"; move to env: map

Locations:

- `action.yml:48`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.komac-version }}" appears directly in run: block of step "Install Komac"; move to env: map

Locations:

- `action.yml:51`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.custom-fork-owner }}" appears directly in run: block of step "Set Custom Environment Variables"; move to env: map

Locations:

- `action.yml:57`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.custom-fork-owner }}" appears directly in run: block of step "Set Custom Environment Variables"; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.custom-tool }}" appears directly in run: block of step "Set Custom Environment Variables"; move to env: map

Locations:

- `action.yml:60`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.custom-tool }}" appears directly in run: block of step "Set Custom Environment Variables"; move to env: map

Locations:

- `action.yml:61`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.custom-tool-url }}" appears directly in run: block of step "Set Custom Environment Variables"; move to env: map

Locations:

- `action.yml:63`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.custom-tool-url }}" appears directly in run: block of step "Set Custom Environment Variables"; move to env: map

Locations:

- `action.yml:64`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.args }}" appears directly in run: block of step "Run Komac"; move to env: map

Locations:

- `action.yml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, missing-permissions, static-inline-injection

**Notes:**

Fixed all findings in action.yml and workflow files:

1. script-injection / static-inline-injection (action.yml): Moved all ${{ inputs.* }} expressions out of run: blocks into env: blocks. 'Install Komac' step uses KOMAC_VERSION env var. 'Set Custom Environment Variables' step uses INPUT_CUSTOM_FORK_OWNER, INPUT_CUSTOM_TOOL, INPUT_CUSTOM_TOOL_URL env vars. 'Run Komac' step uses INPUT_ARGS env var, tokenized via xargs into a bash array to safely handle the args list input.

2. github-env-injection (action.yml): Each value written to $GITHUB_ENV is now sanitized with `printf '%s' "$VAR" | tr -d '\n\r'` before writing.

3. unpinned-uses: Pinned all 5 unpinned action references to full 40-char commit SHAs with tag comments preserved.

4. missing-permissions: Added `permissions: {}` top-level and minimal job-level permissions to test.yml (contents: read) and versioning.yml (contents: write for the tagger action).

