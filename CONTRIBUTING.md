# Contributing to shared-workflows

This repo holds reusable GitHub Actions workflows (`.github/workflows/`) and a shared Renovate preset (`default.json`) that other repos call with a thin `uses:` or `extends:` reference. Bug reports, fixes and doc corrections are welcome.

## Setup

There is nothing to install or build. The repo is YAML workflows plus one JSON preset.

## Before you open a PR

CI (`.github/workflows/lint.yml`) runs [actionlint](https://github.com/rhysd/actionlint) through `raven-actions/actionlint@v2` on every push to `main` and every pull request. Run it from the repo root:

```bash
actionlint
```

If you have Go but not the binary:

```bash
go run github.com/rhysd/actionlint/cmd/actionlint@latest .github/workflows/*.yml
```

actionlint also reports shellcheck findings in `run:` blocks when shellcheck is installed, so install it too.

There is no test suite. Exercise your change by calling the workflow from your fork's branch in a repo of your own, for example `uses: <you>/shared-workflows/.github/workflows/node-ci.yml@<branch>`, and link the run in your PR.

## Conventions

- Consumers call these workflows by `@main` as well as by the `v1` tag, so a merge to `main` reaches every `@main` caller on its next run. Treat each change as a production deploy.
- Keep existing callers working. A new input gets a default that preserves the old behavior, as `run-typecheck` and `tests-before-build` in `node-ci.yml` do.
- Do not rename or remove a workflow file: consumers reference it by path.
- Keep the workflow table in `README.md` in sync with the files in `.github/workflows/`, in the same commit.
- Changes to `default.json` (schedule, grouping, automerge) reach every consumer's Renovate silently. State the intent and the effect in the PR description.
- Commit messages follow Conventional Commits (`feat:`, `fix:`, `ci:`, `docs:`, `chore:`), with an optional scope such as `feat(node-ci):`.
- There is no CHANGELOG. Describe the consumer-visible effect of your change in the PR description.

## Security issues

Do not report vulnerabilities in public issues. Follow [SECURITY.md](SECURITY.md).

## License

This project is released under the MIT License (see [LICENSE](LICENSE)). By contributing, you agree that your contributions are licensed under it.
