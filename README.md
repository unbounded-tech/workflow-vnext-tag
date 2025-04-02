# workflow-release

Expects a Deploy Token to be set up for the repo in order to trigger additional release pipelines.

## Examples

### Rust

*.github/workflows/version-and-tag.yaml*
```yaml
name: version-and-tag
on:
  push:
    branches:
    - main

jobs:

  release:
    uses: harmony-labs/workflow-release/.github/workflows/version-and-tag.yaml@main
    secrets: inherit
    with:
      rust: true
```

### Node

*.github/workflows/version-and-tag.yaml*
```yaml
name: version-and-tag
on:
  push:
    branches:
    - main

jobs:

  release:
    uses: harmony-labs/workflow-release/.github/workflows/version-and-tag.yaml@main
    secrets: inherit
    with:
      node: true
```

### YAML

Any yaml can be patched - such as a values file for a helm chart.

*.github/workflows/version-and-tag.yaml*
```yaml
name: version-and-tag
on:
  push:
    branches:
    - main

jobs:

  release:
    uses: harmony-labs/workflow-release/.github/workflows/version-and-tag.yaml@main
    secrets: inherit
    with:
      yqPatches: |
        patches:
          - filePath: helm/values.yaml
            selector: .image.tag
            valuePrefix: "v"
          - filePath: other/example.yaml
            selector: .image.tag
            valuePrefix: ""
```

### Regex

We can also patch any file with regex:

*.github/workflows/version-and-tag.yaml*
```yaml
name: version-and-tag
on:
  push:
    branches:
    - main

jobs:

  release:
    uses: harmony-labs/workflow-release/.github/workflows/version-and-tag.yaml@main
    secrets: inherit
    with:
      regexPatches: |
        patches:
          - filePath: package/composition.yaml
            regex: /ghcr.io/org-name/package-name:(.*)/g
            valuePrefix: ghcr.io/org-name/package-name:v
          - filePath: package/example.yaml
            regex: /ghcr.io/org-name/package-name:(.*)/g
            valuePrefix: ""
```
