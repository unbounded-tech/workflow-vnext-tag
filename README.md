# workflow-vnext-tag

This repository offers a shared Github Actions Workflow that implements a universal approach to versioning, tagging, and changelogs in a language-agnostic way by using the `vnext` CLI tool.

## Overview

- **Automated Versioning:**  
  Automatically calculates the next semantic version based on commit history using the `vnext` tool.

- **Language-Agnostic Updates:**  
  Updates version references in various file types using flexible patching mechanisms (YQ and Regex).

## Installing vnext

The `vnext` CLI tool can be installed using `ubi`:

```bash
# Install ubi
mkdir -p ~/.ubi/bin
echo 'export PATH="$HOME/.ubi/bin:$PATH"' >> ~/.zshrc  # or your preferred shell profile

# Install vnext with ubi
ubi --project unbounded-tech/vnext --in ~/.ubi/bin
```

## GitHub Actions Workflow

The GitHub Actions workflow accepts the following inputs:

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `changelog` | boolean | No | `true` | Generate and save a changelog based on commit history |
| `node` | boolean | No | `false` | Update version in package.json for Node.js projects |
| `regexPatches` | string | No | | Apply regex-based patches to update version references (see details below) |
| `runs-on` | string | No | `ubuntu-latest` | The type of runner to use for this job |
| `rust` | boolean | No | `false` | Update version in Cargo.toml for Rust projects |
| `yqPatches` | string | No | | Apply YQ-based patches to update version in YAML files (see details below) |
| `useDeployKey` | boolean | No | `false` | Use SSH deploy key for git operations |
| `usePAT` | boolean | No | `false` | Use GitHub Personal Access Token for git operations |

### Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `DEPLOY_KEY` | No | SSH deploy key for git operations when `useDeployKey` is true |
| `GH_PAT` | No | GitHub Personal Access Token with repo,write:packages scopes when `usePAT` is true |

> **Note:** This workflow creates a tag, which can trigger other workflows. The built-in `GITHUB_TOKEN` does not allow a workflow to trigger another workflow, so you need to use either a deploy key or a GitHub personal access token. We recommend using a deploy key per repo as they can be generated, forgotten, and rotated easily.

## Adapting for Other CI/CD Systems

The core functionality relies on the `vnext` CLI tool, which can be installed and used in any CI/CD system. The general steps are:

1. Install `ubi` and use it to install `vnext`
1. Use `vnext --current` to get the current version
1. Use `vnext` to calculate the next version
1. Make a gate, if current == next, exit, otherwise continue
1. Generate a changelog with `vnext --changelog` if needed
1. Update version references in files (using YQ, regex, or language-specific tools)
1. Commit changes and create a new tag
1. Push the tag to trigger release workflows

## Language-Agnostic Patch Options

For optimal performance and flexibility, this workflow provides two language-agnostic methods to update version references in your files: YQ Patches and Regex Patches.

### YQ Patches

YQ Patches allow you to update version references in YAML files using [YQ](https://github.com/mikefarah/yq), a lightweight and portable command-line YAML processor.

**Input Format:**
```yaml
yqPatches: |
  patches:
    - filePath: path/to/file.yaml
      selector: .path.to.property
      valuePrefix: optional-prefix-
    - filePath: another/file.yaml
      selector: .another.property
      valuePrefix: v
```

**Parameters:**
- `filePath`: Path to the YAML file to update
- `selector`: YQ selector expression pointing to the property to update
- `valuePrefix`: Prefix to add before the version number - not optional, set to empty string "" if not needed.

**Version Substitution:**
The workflow automatically substitutes the calculated version number (e.g., `0.1.0`) at the specified location. The `valuePrefix` parameter allows you to customize what appears before the version number:

- Without prefix: `0.1.0` → `0.1.0`
- With prefix `v`: `0.1.0` → `v0.1.0`
- With prefix `ghcr.io/org/package:v`: `0.1.0` → `ghcr.io/org/package:v0.1.0`

**Example:**
```yaml
yqPatches: |
  patches:
    - filePath: .gitops/deploy/helm/values.yaml
      selector: .image.tag
      valuePrefix: ""
    - filePath: .gitops/deploy/helm/Chart.yaml
      selector: .version
      valuePrefix: ""
    - filePath: config.yaml
      selector: .spec.package
      valuePrefix: ghcr.io/org/package:v
```

This will:
1. Update `.image.tag` in `.gitops/deploy/helm/values.yaml` to the new version (e.g., `0.1.0` → `0.1.0`)
2. Update `.version` in `.gitops/deploy/helm/Chart.yaml` to the new version (e.g., `0.1.0` → `0.1.0`)
3. Update `.spec.package` in `config.yaml` to include the prefix and new version (e.g., `0.1.0` → `ghcr.io/org/package:v0.1.0`)

### Regex Patches

Regex Patches provide a more flexible approach to update version references in any text-based file using regular expressions.

**Input Format:**
```yaml
regexPatches: |
  patches:
    - filePath: path/to/file
      regex: pattern-to-match
      valuePrefix: replacement-prefix
```

**Parameters:**
- `filePath`: Path to the file to update
- `regex`: Regular expression pattern to match (use `/pattern/g` format for global replacement)
- `valuePrefix`: Prefix to add before the version number in the replacement

**Version Substitution:**
The workflow automatically substitutes the calculated version number (e.g., `0.1.0`) in the replacement. The `valuePrefix` parameter allows you to customize what appears before the version number:

- With prefix `v`: `0.1.0` → `v0.1.0`
- With prefix `ghcr.io/org-name/package-name:v`: `0.1.0` → `ghcr.io/org-name/package-name:v0.1.0`
- With prefix `Current version: v`: `0.1.0` → `Current version: v0.1.0`

**Example:**
```yaml
regexPatches: |
  patches:
    - filePath: package/composition.yaml
      regex: /ghcr.io/org-name/package-name:(.*)/g
      valuePrefix: ghcr.io/org-name/package-name:v
    - filePath: README.md
      regex: /Current version: v[0-9]+\.[0-9]+\.[0-9]+/g
      valuePrefix: Current version: v
```

This will:
1. Replace all occurrences matching the regex in `package/composition.yaml` with the prefix plus the new version (e.g., `0.1.0` → `ghcr.io/org-name/package-name:v0.1.0`)
2. Replace all occurrences matching the regex in `README.md` with the prefix plus the new version (e.g., `0.1.0` → `Current version: v0.1.0`)

## Example Usage

### GitHub Actions Example

```yaml
on:
  push:
    branches:
      - main

jobs:
  quality:
    # Your quality checks here
    
  version-and-tag:
    needs: quality
    uses: unbounded-tech/workflow-vnext-tag/.github/workflows/workflow-simplified.yaml@main
    secrets: inherit
    with:
      useDeployKey: true
      yqPatches: |
        patches:
          - filePath: deploy/values.yaml
            selector: .image.tag
            valuePrefix: v
          - filePath: deploy/Chart.yaml
            selector: .version
            valuePrefix: v
```

### Full Rust Example (GitHub Actions)

To understand how the workflow fits into the larger picture, here is a full rust example.

First, set up a deploy key for the repo - you can use the vnext CLI to do so:

```
vnext generate-deploy-key
```

Then, on pushes to your trunk branch (main/master):

```yaml
on:
  push:
    branches:
      - main
      - master

jobs:
  quality:
    uses: unbounded-tech/workflow-rust-quality/.github/workflows/workflow.yaml@main
    with:
      cargo_test_args: '--verbose'
      lint: true

  version-and-tag:
    needs: quality
    uses: unbounded-tech/workflow-vnext-tag/.github/workflows/workflow-simplified.yaml@main
    secrets: inherit
    with:
      useDeployKey: true
      rust: true
```

### Releasing Rust Binaries

The Version and tag job creates a tag - make sure to set up a deploy key for your repo and a secret to use it via the `vnext` CLI.

The `release.yaml` workflow is designed for building and releasing Rust binaries across multiple platforms using a matrix strategy. It helps you publish artifacts as GitHub Releases with ease. It picks up where version-and-tag leaves off.

**Example Trigger:**
```yaml
on:
  push:
    tags:
      - "v*.*.*"

jobs:
  publish:
    uses: unbounded-tech/workflow-rust-release/.github/workflows/workflow.yaml@main
    with:
      executable_name: my-app
      platforms: '[{"os-name": "Linux-x86_64", "runs-on": "ubuntu-24.04", "target": "x86_64-unknown-linux-musl"}]'
