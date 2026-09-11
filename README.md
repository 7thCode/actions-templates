# actions-templates

Reusable GitHub Actions workflows shared across projects, so a fix or improvement
lands in every consuming repository at once instead of being copy-pasted per repo.

## Workflows

### `multiplatform-release.yml`

Builds an Electron (or similar npm-based) app on macOS/Windows/Linux, and:

- on a `v*` tag push, packages and publishes installers directly to the matching
  GitHub Release (`electron-builder --publish always`)
- on `workflow_dispatch`, packages without publishing and uploads workflow
  artifacts instead, optionally backfilling an existing release via
  `upload_to_release_tag`

#### Usage

```yaml
# .github/workflows/build.yml (in the consuming repo)
name: Build

on:
  push:
    tags:
      - 'v*'
  workflow_dispatch:
    inputs:
      upload_to_release:
        description: 'Existing release tag to upload built assets to (optional)'
        required: false
        type: string

permissions:
  contents: write

jobs:
  build:
    uses: 7thCode/actions-templates/.github/workflows/multiplatform-release.yml@main
    with:
      upload_to_release_tag: ${{ inputs.upload_to_release }}
    secrets:
      GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

All inputs (`node_version`, `package_manager`, `build_command`, `artifact_globs`,
`upload_to_release_tag`) are optional and default to the settings used by
[hugging-Loader](https://github.com/7thCode/hugging-loader).

Pin `@main` to a tag (e.g. `@v1`) once this repo has releases, if a consuming
project needs to freeze the workflow version.
