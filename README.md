# Semantic Release GitHub Action Workflow

This GitHub Actions workflow automates the process of creating semantic releases and adding release notes to pull requests. It uses the "mathieu-keller/github-action-workflows" repository's semantic release action.

## Workflow Configuration

### YAML Configuration

To use this workflow, add the following YAML configuration to your repository's `.github/workflows` directory in a file, e.g., `semantic-release.yml`:

```yaml
name: Semantic Release

on:
  push:
    branches:
      - main
  pull_request:

permissions:
  contents: write
  issues: write
  pull-requests: write

jobs:
  dry:
    name: dry release
    uses: mathieu-keller/github-action-workflows/.github/workflows/semantic-release.yaml@main
    if: ${{ github.ref != 'refs/heads/main' }}
    with:
      release-branch: main
      dry: true
  show-outputs:
    needs: [ dry ]
    runs-on: ubuntu-22.04
    steps:
      - name: echo
        run: |
          echo "${{needs.dry.outputs.has_new_version}}"
          echo "${{needs.dry.outputs.new_version}}"
          echo "${{needs.dry.outputs.release_notes}}"
  release:
    name: create release
    if: ${{ github.ref == 'refs/heads/main' }}
    uses: mathieu-keller/github-action-workflows/.github/workflows/semantic-release.yaml@main
    with:
      release-branch: main
      dry: false
```

This configuration triggers the workflow on pushes to the `main` branch and uses the semantic release action with the specified options.

### Action Parameters

- `dry`: A boolean parameter that controls whether the action runs in dry-run mode. When set to `true`, no actual releases are created, and the action simulates the release process.
- `release-branch`: The branch where releases are created. This parameter allows you to specify the branch for creating releases. The default is set to `main`.

### Action Outputs

- `has_new_version`: A boolean value indicating if a new version is created or can be created.
- `new_version`: If `has_new_version` is true, this output contains the new version. If the release is not on the specified release branch, the version will be the branch name plus the commit hash.
- `release_notes`: If `has_new_version` is true, this output contains the release notes in markdown format.

## Example Usage

1. Push changes to the `main` branch or to an pull request.
2. The workflow will trigger, and if conditions are met, it will create a semantic release, or the release notes will be added to the pull requests.

## Contributing

If you encounter issues or have suggestions for improvements, feel free to [open an issue](https://github.com/mathieu-keller/github-action-workflows/issues) or [create a pull request](https://github.com/mathieu-keller/github-action-workflows/pulls).
