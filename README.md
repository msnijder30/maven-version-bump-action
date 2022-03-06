# Maven Version Bump Action

A simple GitHub Actions to bump the version of Maven projects.

When triggered, this action will look at the commit message of HEAD~1 and determine if it contains one of `#major`, `#minor`, or `#patch` (in that order of precedence).
If true, it will use Maven to bump your pom's version.

For example, a `#minor` update to version `1.3.9` will result in the version changing to `1.4.0`.
The change will then be committed.

# Difference with original repo
This fork makes the bump mode configurable through the input field `bump-mode` rather than looking at the previous commit.
This can be useful if you want to create releases manually with the `workflow_dispatch` workflow.

### Example usage:

```yaml
name: Release

on:
  workflow_dispatch:
  inputs:
    bump-mode:
      description: 'Bump mode'
      required: true
      default: 'none'
      type: choice
      options:
        - major
        - minor
        - patch

jobs:
  build:

    runs-on: ubuntu-latest
...

    - name: Bump Version
      id: bump
      uses: msnijder30/maven-version-bump-action@v5
      with:
          github-token: ${{ secrets.github_token }}
          bump-mode: ${{ github.event.inputs.bump-mode }}
```

## Sample Usage

```yaml
name: Clojure Version Bump

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout Latest Commit
      uses: actions/checkout@v2

    - name: Bump Version
      id: bump
      uses: nnichols/maven-version-bump-action@v3
      with:
        github-token: ${{ secrets.github_token }}

    - name: Print Version
      run: "echo 'New Version: ${{steps.bump.outputs.version}}'"
```

## Supported Arguments

* `github-token`: The only required argument. Can either be the default token, as seen above, or a personal access token with write access to the repository
* `git-email`: The email address each commit should be associated with. Defaults to a github provided noreply address
* `git-username`: The GitHub username each commit should be associated with. Defaults to `github-actions[bot]`
* `pom-path`: The path within your directory the pom.xml you intended to change is located.

## Outputs

* `version` - The after-bump version. Will return the old version if bump was not necessary.

## Licensing

Copyright © 2021-2022 [Nick Nichols](https://nnichols.github.io/)

Distributed under the [MIT License](https://github.com/nnichols/maven-version-bump-action/blob/master/LICENSE)
