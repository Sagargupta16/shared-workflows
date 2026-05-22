# Sagargupta16/shared-workflows

Reusable GitHub Actions workflows for all my repos. Call them from any repo's CI with a thin `uses:` reference.

## Available workflows

| Workflow | Purpose | Inputs |
|----------|---------|--------|
| `node-ci.yml` | Install + lint + build + test (auto-detects pnpm/npm/yarn) | `node-version`, `run-tests`, `run-lint`, `working-directory` |
| `python-ci.yml` | uv sync + ruff check + ruff format + pytest | `python-version`, `working-directory`, `run-tests` |
| `deploy-gh-pages.yml` | Build + deploy to GitHub Pages | `node-version`, `build-command`, `output-dir`, `working-directory` |
| `docker-build-scan.yml` | Docker buildx + Trivy SARIF scan (no push) | `context`, `dockerfile`, `trivy-severity` |
| `security-scan.yml` | Dependency Review + Trivy filesystem scan | `fail-on-severity` |
| `terraform-ci.yml` | fmt + validate + Checkov SARIF | `working-directory`, `terraform-version` |
| `release.yml` | Tag-triggered build + GitHub Release with changelog | `node-version`, `build-command`, `artifact-path` |

## Usage

```yaml
# .github/workflows/ci.yml in any consumer repo
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  build:
    uses: Sagargupta16/shared-workflows/.github/workflows/node-ci.yml@main

  deploy:
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    needs: build
    uses: Sagargupta16/shared-workflows/.github/workflows/deploy-gh-pages.yml@main
    with:
      output-dir: dist

  security:
    uses: Sagargupta16/shared-workflows/.github/workflows/security-scan.yml@main
    permissions:
      contents: read
      security-events: write
```

## Design principles

- **Auto-detect** package manager from lockfile (no manual config)
- **Skip gracefully** if a script doesn't exist (no `lint` script? skip lint step)
- **Minimal inputs** with sensible defaults — most repos need zero config
- **Security by default** — Trivy + Dependency Review on every workflow
