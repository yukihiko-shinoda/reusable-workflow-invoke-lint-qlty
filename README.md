# reusable-workflow-invoke-lint-qlty

[![Dependabot](https://flat.badgen.net/github/dependabot/yukihiko-shinoda/reusable-workflow-invoke-lint-qlty?icon=dependabot)](https://github.com/yukihiko-shinoda/reusable-workflow-invoke-lint-qlty/security/dependabot)

The [Qlty] coverage workflow that can be reused powered by [invoke-lint].

## Advantage

1. Centralizes the test + coverage pipeline
2. Zero-config OIDC authentication to Qlty

### 1. Centralizes the test + coverage pipeline

Running tests and uploading coverage to [Qlty] from a project using [invoke-lint] requires the same multi-step setup in every repository: install uv, sync dependencies, run the invoke task, and upload the result.

This reusable workflow encapsulates all those steps. Update the workflow once and all consuming repositories pick up the change automatically.

### 2. Zero-config OIDC authentication to Qlty

Coverage upload authenticates with [Qlty] via OIDC. No static token or secret needs to be stored in the calling repository.

## Quickstart

Add the following job to any repository that uses [invoke-lint] and has a `test.coverage` invoke task producing `coverage.xml`:

```yaml
# For OIDC token exchange with Qlty:
# - Configuring OpenID Connect in cloud providers - GitHub Docs
#   https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-cloud-providers#adding-permissions-settings
permissions:
  contents: read
  id-token: write
jobs:
  analyze:
    uses: yukihiko-shinoda/reusable-workflow-invoke-lint-qlty/.github/workflows/workflow.yml@v1
    permissions:
      contents: read
      id-token: write
```

<!-- markdownlint-disable no-trailing-punctuation -->
## How do I...

### How do I change the Python version?
<!-- markdownlint-enable no-trailing-punctuation -->

Pass the `python-version` input:

```yaml
permissions:
  contents: read
  id-token: write
jobs:
  analyze:
    uses: yukihiko-shinoda/reusable-workflow-invoke-lint-qlty/.github/workflows/workflow.yml@v1
    with:
      python-version: '3.12'
    permissions:
      contents: read
      id-token: write
```

<!-- markdownlint-disable-next-line no-trailing-punctuation -->
### How do I use this in a monorepo?

Pass the `add-prefix` input to prepend a directory path to coverage file paths. [Qlty] uses this to map coverage entries back to the correct files in a monorepo layout:

```yaml
permissions:
  contents: read
  id-token: write
jobs:
  analyze:
    uses: yukihiko-shinoda/reusable-workflow-invoke-lint-qlty/.github/workflows/workflow.yml@v1
    with:
      add-prefix: 'my-package/'
    permissions:
      contents: read
      id-token: write
```

## API

### Inputs

| Name | Type | Default | Description |
| --- | --- | --- | --- |
| `python-version` | string | `'3.14'` | Python version passed to [`astral-sh/setup-uv`]. |
| `add-prefix` | string | `''` | Path prefix prepended to coverage file paths by [`qltysh/qlty-action/coverage`]. Use for monorepo layouts. |

[invoke-lint]: https://github.com/yukihiko-shinoda/invoke-lint
[Qlty]: https://qlty.sh
[`astral-sh/setup-uv`]: https://github.com/astral-sh/setup-uv
[`qltysh/qlty-action/coverage`]: https://github.com/qltysh/qlty-action
