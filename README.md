# workflow-release

Expects a Personal Access Token to be set as an actions secret named `GH_PAT_TOKEN` which has permissions for:
* repo
* write:packages

You can generate this token here: https://github.com/settings/tokens/new?scopes=repo,write:packages

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
