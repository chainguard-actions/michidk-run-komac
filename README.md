<h1> <img src="https://rawcdn.githack.com/michidk/run-komac/86f4bad6701cb130ce85c4505ad39d9bbcd3d919/.github/images/github-actions-logo.png" width="32" height="32" alt="Logo" /> Run Komac (GitHub Action) <a href="https://github.com/russellbanks/Komac"> <img src="https://rawcdn.githack.com/michidk/run-komac/86f4bad6701cb130ce85c4505ad39d9bbcd3d919/.github/images/komac-logo.svg" height="24px" style="vertical-align:bottom" alt="Komac logo" /> </a></h1>

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/michidk/run-komac?logo=github)](https://github.com/michidk/run-komac/releases)
[![GitHub](https://img.shields.io/github/license/michidk/run-komac?logo=gnu)](https://github.com/michidk/run-komac?tab=MIT-1-ov-file#readme)

This GitHub Action installs Komac, and then runs a user-specified command with Komac.

This action is used in the [winget-updater](https://github.com/michidk/winget-updater/) GitHub action to automatically update WinGet packages.


## 📖 Example Usage

```yaml
name: My Workflow
on: workflow_dispatch

jobs:
  my_job:
    runs-on: ubuntu-latest
    steps:
    - name: Run Komac
      uses: michidk/run-komac@v2
      with:
        args: '--version'
```

## ⚒️ Configuration Options

- `komac-version`: Specifies which version of Komac to use.
  - **Required**: ❌
  - **Default**: `latest`
- `args`: The command to run with Komac.
  - **Required**: ✅
- `custom-fork-owner`: Custom fork owner.
  - **Required**: ❌
- `custom-tool`: Custom tool.
  - **Required**: ❌
- `custom-tool-url`: Custom tool URL.
  - **Required**: ❌

## Privacy

This Action contacts Chainguard's licensing server to verify authorization. Connection metadata (IP address, GitHub repository identifier, timestamp, and any metadata encoded in the auth token) is transmitted to Chainguard, Inc. even if authorization is denied in accordance with our [Privacy Notice](https://www.chainguard.dev/legal/privacy-notice)
