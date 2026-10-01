<!-- markdownlint-disable -->

# Hardening Report: michidk--run-komac/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **michidk--run-komac/v2.1.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action uses `cargo-bins/cargo-binstall@main`, which is pinned to a mutable branch name rather than an immutable 40-character commit SHA. This means the referenced action can be silently changed by the upstream repository at any time, enabling supply-chain attacks.

Locations:

- `action.yml:36`

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ inputs.* }}` expressions into shell command strings (sub-rule a), allowing an attacker who controls those inputs to inject arbitrary shell commands.

1. "Install Komac" step (line 40): `if [ "${{ inputs.komac-version }}" == 'latest' ]` — direct expression interpolation in a shell conditional.
2. "Install Komac" step (line 42): `cargo binstall komac@${{ inputs.komac-version }} -y` — direct, unquoted interpolation into a shell command.
3. "Set Custom Environment Variables" step (lines 47–55): `${{ inputs.custom-fork-owner }}`, `${{ inputs.custom-tool }}`, and `${{ inputs.custom-tool-url }}` are interpolated directly into `[[ -n "..." ]]` guards and `echo` commands.
4. "Run Komac" step (line 58): `komac ${{ inputs.args }}` — direct, unquoted interpolation of a required input into a shell command, giving full shell command injection to any caller.

Locations:

- `action.yml:40`
- `action.yml:42`
- `action.yml:47`
- `action.yml:58`

### github-env-injection (severity: high)

The "Set Custom Environment Variables" step writes `inputs.*` values directly to `$GITHUB_ENV` without the required sanitization step (`printf '%s' "$VAR" | tr -d '\n\r'`). An attacker-controlled input containing newlines can inject arbitrary environment variable definitions (e.g. `ACTIONS_RUNTIME_TOKEN=...`) into the runner environment for all subsequent steps.

Affected writes:
- `echo "KOMAC_FORK_OWNER=${{ inputs.custom-fork-owner }}" >> $GITHUB_ENV`
- `echo "KOMAC_CREATED_WITH=${{ inputs.custom-tool }}" >> $GITHUB_ENV`
- `echo "KOMAC_CREATED_WITH_URL=${{ inputs.custom-tool-url }}" >> $GITHUB_ENV`

Locations:

- `action.yml:49`
- `action.yml:52`
- `action.yml:55`

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

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, static-inline-injection

**Notes:**

Fixed all findings in hardened/action/action.yml:
1. Pinned cargo-bins/cargo-binstall@main to SHA f8162f41a8ea5ec653c5cba967cc1b2d89c867c5.
2. Moved all ${{ inputs.* }} expressions to env: blocks: komac-version→KOMAC_VERSION, custom-fork-owner→CUSTOM_FORK_OWNER, custom-tool→CUSTOM_TOOL, custom-tool-url→CUSTOM_TOOL_URL, args→INPUT_ARGS.
3. Sanitized all three GITHUB_ENV writes with printf '%s' | tr -d '\n\r' to prevent newline injection.
4. Used xargs-based bash array tokenization for inputs.args to properly split the argument list while preserving argument boundaries.

