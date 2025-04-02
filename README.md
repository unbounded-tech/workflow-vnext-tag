# Workflow for Any Project With Releases

This repository offers a GitHub workflows which handles versioning, and tagging in a language agnostic way, using `vnext`. It can be used along with some of our other workflows.

---

## Overview

- **Quality Assurance:**  
  Automated builds, tests, linting, and formatting checks ensure your code remains robust and maintainable. Efficient caching strategies further reduce build times.

- **Release Management:**  
  While this repository manages CI and quality checks, you can integrate it with the separate [harmony-labs/workflow-release](https://github.com/harmony-labs/workflow-release) project to automate version bumps, tag creation, and GitHub Releases. In particular, the `release.yaml` workflow is a powerful tool for building and releasing Rust binaries across multiple platforms.

---

## Key Workflows & Their Triggers

### Quality Pipeline

Run your code quality checks automatically on code updates.

**Example:**
```yaml
on:
  push:
    branches:
      - main

jobs:
  quality:
    uses: harmony-labs/workflow-rust-quality/.github/workflows/quality.yaml@main
    with:
      cargo_test_args: '--verbose'
      lint: true
```

---

### Quality, Versioning & Tagging

Use with other harmony-labs workflows to automatically generate versions and create Git tags based on commit logs, ensuring that only meaningful changes trigger a version bump.

**Example:**
```yaml
on:
  push:
    branches:
      - main

jobs:
  quality:
    uses: harmony-labs/workflow-rust-quality/.github/workflows/workflow.yaml@main
    with:
      cargo_test_args: '--verbose'
      lint: true

  version-and-tag:
    needs: quality
    uses: harmony-labs/workflow-vnext-tag/.github/workflows/workflow.yaml@main
    secrets: inherit
    with:
      useDeployKey: true
      rust: true
```

And then add the following release job.

### Releasing Rust Binaries

The Version and tag job creates a tag - make sure to set up a deploy key for your repo and a secret to use it (will automate later).

The `release.yaml` workflow is designed for building and releasing Rust binaries across multiple platforms using a matrix strategy. It helps you publish artifacts as GitHub Releases with ease. It picks up where version-and-tag leaves off.

**Example Trigger:**
```yaml
on:
  push:
    tags:
      - "v*.*.*"

jobs:
  publish:
    uses: harmony-labs/workflow-rust-release/.github/workflows/workflow.yaml@main
    with:
      executable_name: my-app
      platforms: '[{"os-name": "Linux-x86_64", "runs-on": "ubuntu-24.04", "target": "x86_64-unknown-linux-musl"}]'
```

---

## Getting Started

1. **Integrate the Workflows:**  
   Copy the example snippets into your project’s `.github/workflows` directory. Adjust the inputs to match your project's specific needs.

2. **Enhance Your Release Process:**  
   For a full release management experience—including automated versioning, tagging, and artifact publishing—integrate the separate [harmony-labs/workflow-release](https://github.com/harmony-labs/workflow-release) project with these workflows.

---

## Best Practices

- **Run Early Quality Checks:**  
  Execute the quality pipeline on every push to ensure issues are caught before merging.

- **Automate Versioning:**  
  Leverage commit messages to drive your versioning strategy, minimizing manual intervention and errors.

- **Support Multiple Platforms:**  
  Use the `release.yaml` workflow’s matrix strategy to build and release your Rust binaries on all major platforms.

- **Keep Workflows Modular:**  
  Separate continuous integration from release management. This modularity simplifies maintenance and allows each process to excel in its role.

---

Enhance your Rust development workflow with these GitHub actions and the complementary [harmony-labs/workflow-release](https://github.com/harmony-labs/workflow-release) project. Enjoy robust quality assurance and seamless cross-platform releases every time you push new code!
