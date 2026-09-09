<!-- markdownlint-disable -->

# Hardening Report: michidk--run-komac/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **michidk--run-komac/v2.1.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `${{ inputs.* }}` expressions are interpolated directly inside `run:` shell command strings, violating rule (a). An attacker controlling these inputs can inject arbitrary shell commands.

- Line 47: `if [ "${{ inputs.komac-version }}" == 'latest' ]; then` — direct expression in run block
- Line 49: `cargo binstall komac@${{ inputs.komac-version }} -y` — direct expression, also unquoted (rule b)
- Line 54: `if [[ -n "${{ inputs.custom-fork-owner }}" ]]; then` — direct expression in run block
- Line 55: `echo "KOMAC_FORK_OWNER=${{ inputs.custom-fork-owner }}" >> $GITHUB_ENV` — direct expression in run block
- Line 57: `if [[ -n "${{ inputs.custom-tool }}" ]]; then` — direct expression in run block
- Line 58: `echo "KOMAC_CREATED_WITH=${{ inputs.custom-tool }}" >> $GITHUB_ENV` — direct expression in run block
- Line 60: `if [[ -n "${{ inputs.custom-tool-url }}" ]]; then` — direct expression in run block
- Line 61: `echo "KOMAC_CREATED_WITH_URL=${{ inputs.custom-tool-url }}" >> $GITHUB_ENV` — direct expression in run block
- Line 65: `run: komac ${{ inputs.args }}` — direct expression in run block, completely unquoted

Locations:

- `action.yml:47`
- `action.yml:49`
- `action.yml:54`
- `action.yml:55`
- `action.yml:57`
- `action.yml:58`
- `action.yml:60`
- `action.yml:61`
- `action.yml:65`

### github-env-injection (severity: high)

Three `run:` steps write `${{ inputs.* }}` values directly into `$GITHUB_ENV` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). A newline character in any of these inputs can inject arbitrary environment variables into subsequent steps.

- Line 55: `echo "KOMAC_FORK_OWNER=${{ inputs.custom-fork-owner }}" >> $GITHUB_ENV`
- Line 58: `echo "KOMAC_CREATED_WITH=${{ inputs.custom-tool }}" >> $GITHUB_ENV`
- Line 61: `echo "KOMAC_CREATED_WITH_URL=${{ inputs.custom-tool-url }}" >> $GITHUB_ENV`

Locations:

- `action.yml:55`
- `action.yml:58`
- `action.yml:61`

### unpinned-uses (severity: high)

The step 'Install binstall' uses `cargo-bins/cargo-binstall@main`, which is pinned to a mutable branch name rather than an immutable 40-character commit SHA. This means the action can be silently updated to a malicious version without any change to this file, creating a supply-chain attack risk.

Locations:

- `action.yml:41`

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

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection, github-env-injection

**Notes:**

Fixed all 12 findings in action.yml:
1. Pinned cargo-bins/cargo-binstall@main to full SHA b874e25ea559687bec77e281e9b271aa1367b624.
2. Moved all ${{ inputs.* }} expressions from run: blocks into env: maps (komac-version→KOMAC_VERSION, custom-fork-owner→CUSTOM_FORK_OWNER, custom-tool→CUSTOM_TOOL, custom-tool-url→CUSTOM_TOOL_URL, args→INPUT_ARGS).
3. Sanitized all three GITHUB_ENV writes using printf '%s' | tr -d '\n\r' to prevent newline injection.
4. Handled inputs.args as a whitespace-separated argument list using the xargs/while-read-NUL bash array tokenization pattern to preserve argument boundaries without injection risk.

