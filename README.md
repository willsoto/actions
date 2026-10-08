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
- If `publishEnabled` is `true`, a [trusted publisher](https://docs.npmjs.com/trusted-publishers) configured on npmjs.com for the package. Use GitHub Actions as the provider and enter the **calling** workflow's filename (e.g. `node-release.yml` in the consuming repository), since npm validates the caller rather than this reusable workflow. No npm token is needed.
- The calling job must grant `id-token: write` (alongside `contents: write` and `pull-requests: write`); a reusable workflow cannot be granted more permissions than its caller.
- Node 22.14.0 or later in the consuming repository's `.tool-versions`, which ships an npm that supports trusted publishing (11.5.1+).

**Inputs:**

| Name             | Type    | Default | Description               |
| ---------------- | ------- | ------- | ------------------------- |
| `publishEnabled` | boolean | `true`  | Toggle npm publish step   |

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
    permissions:
      contents: write
      pull-requests: write
      id-token: write
    secrets:
      RELEASE_TOKEN: ${{ secrets.RELEASE_TOKEN }}
```

**Example without npm publish:**

```yaml
jobs:
  release:
    uses: willsoto/actions/.github/workflows/node-release.yml@main
    with:
      publishEnabled: false
    secrets:
      RELEASE_TOKEN: ${{ secrets.RELEASE_TOKEN }}
```
