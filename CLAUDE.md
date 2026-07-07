# CLAUDE.md

> This file stacks on top of the workspace root at `C:\Code\GitHub\`:
> - Root [`CLAUDE.md`](../CLAUDE.md) -- voice, rules, routing map, references, skills, slash commands, conventions.
> - Root [`MEMORY.md`](../MEMORY.md) -- live facts across repos.
> - Root [`STATUS.md`](../STATUS.md) -- live PR/CI/security dashboard.
> - [`.claude/resources/`](../.claude/resources/README.md) -- deep reference for collaboration, workflow, git, OSS, debugging, voice.
>
> Read those first. The guidance below only adds **repo-specific context** -- it does not override anything in the root.

## Project

Reusable GitHub Actions workflows + shared Renovate preset consumed by ALL Sagargupta16 repos via thin `uses:` references. Changes here ripple to every consumer's CI on their next run.

## Stack

- **Language**: YAML (GitHub Actions reusable workflows) + JSON (Renovate preset)
- **Framework**: none
- **Database**: none
- **Package manager**: none
- **Deploy target**: consumed directly from `main` via `uses: Sagargupta16/shared-workflows/.github/workflows/<name>.yml@main`

## Run

No build. Validation is consumer-side: a change is "live" the moment it lands on `main`.

## Test

No test suite. Verify by triggering a consumer repo's CI (or `gh workflow run` on a consumer) after merging.

## Entry points

- `.github/workflows/node-ci.yml` -- install + lint + build + test (auto-detects pnpm/npm/yarn)
- `.github/workflows/python-ci.yml` -- uv sync + ruff check/format + pytest
- `.github/workflows/deploy-gh-pages.yml` -- build + deploy to GitHub Pages
- `.github/workflows/docker-build-scan.yml` -- buildx + Trivy SARIF (no push)
- `.github/workflows/security-scan.yml` -- Dependency Review + Trivy filesystem
- `.github/workflows/terraform-ci.yml` -- fmt + validate + Checkov SARIF
- `.github/workflows/release.yml` -- tag-triggered build + GitHub Release
- `renovate.json` -- shared preset: monthly grouped PR (25th, IST), auto-merge on green CI. Consumers use `extends: ['github>Sagargupta16/shared-workflows']`

## Gotchas

- Consumers pin `@main`, so a broken merge here breaks CI across every repo at once. Treat every change as a production deploy.
- README.md workflow table must stay in sync with the actual files in `.github/workflows/` -- update both in the same commit.
- Renovate preset changes propagate silently to all consumers; schedule/automerge edits need explicit intent, never drive-by.

## Repo-specific rules

- Never rename a workflow file without checking all consumer repos for `uses:` references first (`gh search code "shared-workflows/.github/workflows/<name>"`).
