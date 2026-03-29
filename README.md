# actions

Reusable GitHub Actions workflows for Node.js projects.

## Workflows

### node-ci

A reusable CI workflow that runs typecheck, lint, test, and build steps.

**Prerequisites:** Your repository must have:

- A `.tool-versions` file with a `nodejs` entry (used by [asdf](https://asdf-vm.com/))
- A `packageManager` field in `package.json` pointing to pnpm (used by [corepack](https://nodejs.org/api/corepack.html))
- The following pnpm scripts: `typecheck`, `lint`, `test:coverage`, `build`

**Usage:**

```yaml
# .github/workflows/ci.yml
name: "ci"

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  ci:
    uses: willsoto/actions/.github/workflows/node-ci.yml@main
```

**Inputs:**

| Name           | Type    | Default | Description              |
| -------------- | ------- | ------- | ------------------------ |
| `cacheEnabled` | boolean | `true`  | Toggle pnpm store caching |

**Example with inputs:**

```yaml
jobs:
  ci:
    uses: willsoto/actions/.github/workflows/node-ci.yml@main
    with:
      cacheEnabled: false
```

### node-release

Automates releases using [release-please](https://github.com/googleapis/release-please). On every push to `main`, release-please maintains a Release PR that accumulates [conventional commits](https://www.conventionalcommits.org/). Merging the Release PR creates a GitHub release and bumps the version in `package.json`.

**Prerequisites:**

- A `RELEASE_TOKEN` repository secret containing a [Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) with `contents:write` and `pull-requests:write` scopes. A PAT is required (instead of the default `GITHUB_TOKEN`) so that merging release PRs triggers downstream workflows.

**Usage:**

```yaml
# .github/workflows/node-release.yml
name: "node-release"

on:
  push:
    branches:
      - main

jobs:
  release:
    uses: willsoto/actions/.github/workflows/node-release.yml@main
    secrets:
      RELEASE_TOKEN: ${{ secrets.RELEASE_TOKEN }}
```
