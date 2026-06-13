# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This repository provides a reusable GitHub Actions workflow (`workflow_call`) that runs tests via [invoke-lint](https://github.com/yukihiko-shinoda/invoke-lint) and uploads coverage to [Qlty](https://qlty.sh).

## Workflow: `.github/workflows/workflow.yml`

The single workflow file is the entire product. It:

1. Checks out the calling repo and sets up Python/uv via `astral-sh/setup-uv`
2. Runs `uv sync` then `uv run invoke test.coverage --xml` — the calling repo must have an `invoke` task named `test.coverage` that produces `coverage.xml`
3. Uploads `coverage.xml` to Qlty using OIDC authentication (`qltysh/qlty-action/coverage@v2`)

### Inputs

| Input | Default | Purpose |
|-------|---------|---------|
| `python-version` | `'3.14'` | Python version passed to `setup-uv` |
| `add-prefix` | `''` | Path prefix added to coverage file paths (for monorepos) |

## How callers consume this workflow

Minimal usage in a calling repo:

```yaml
jobs:
  analyze:
    uses: yukihiko-shinoda/reusable-workflow-invoke-lint-qlty/.github/workflows/workflow.yml@main
    permissions:
      contents: read
      id-token: write
```

For monorepos, pass `add-prefix: 'my-package/'` under `with:` to prepend a directory path to coverage file paths so Qlty can map them correctly.

The calling repo must have an invoke task `test.coverage` (provided by [invoke-lint](https://github.com/yukihiko-shinoda/invoke-lint)) that produces `coverage.xml` in Cobertura format.

## Conventions

- Action versions (e.g. `actions/checkout@v6`, `astral-sh/setup-uv@v7`) are kept current by Dependabot (weekly, `github-actions` ecosystem).
- The workflow requires `contents: read` and `id-token: write` permissions — `id-token: write` is needed for OIDC token exchange with Qlty.
