# renovate-config

[![CI](https://github.com/grigoriev/renovate-config/actions/workflows/ci.yml/badge.svg)](https://github.com/grigoriev/renovate-config/actions/workflows/ci.yml)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/grigoriev/renovate-config/badge)](https://scorecard.dev/viewer/?uri=github.com/grigoriev/renovate-config)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Shared [Renovate](https://docs.renovatebot.com/) preset for the public repositories under
[grigoriev](https://github.com/grigoriev) and [intechcore](https://github.com/intechcore).
The intechcore repositories use it through
[intechcore/renovate-config](https://github.com/intechcore/renovate-config).

## Usage

Put this in the `renovate.json` of a repository:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>grigoriev/renovate-config"]
}
```

Add only the rules that belong to that repository below `extends`: its own custom
managers, versioning, groups, `lockFileMaintenance`. A repository setting overrides the
preset. Do not repeat a preset setting in a repository.

## What the preset does

| Setting | Effect |
|---|---|
| `config:recommended` | Renovate's recommended base, with the dependency dashboard |
| `helpers:pinGitHubActionDigests` | GitHub Actions are pinned by commit SHA, the version stays in a comment |
| `:semanticCommits`, `:semanticCommitScopeDisabled` | Commits and pull request titles are Conventional Commits without a scope, for example `chore: update github actions` |
| `pinDigests: true` | Docker images are pinned by digest |
| `automerge`, `automergeType: pr`, `platformAutomerge: true` | Renovate opens a pull request and turns on GitHub auto-merge. GitHub merges it as soon as the required checks pass |
| `osvVulnerabilityAlerts: true` | Updates for known vulnerabilities come from the OSV database too |
| `prHourlyLimit: 0`, `prConcurrentLimit: 20` | No hourly limit, up to 20 open pull requests. The limits of `config:recommended` held updates back, and action versions drifted apart between repositories |
| Group `github actions` | Minor, patch and digest updates of GitHub Actions travel in one pull request per repository. Majors stay separate |
| Dashboard approval | Off for every update, majors included. It also overrides an approval gate set on the Mend side |
| `pip-compile` | Covers every compiled `.github/requirements/*.txt` file. A repository needs no own pattern |

GitHub auto-merge is safe here because the ruleset of every repository still applies to it:
required checks, signed commits and the CodeQL rule. A red pull request never merges. The
repository needs **Allow auto-merge** in its settings.

Custom managers:

- **Workflow env images.** A Docker image pinned by tag and digest in a workflow `env`
  variable, below a `# renovate:` comment:

  ```yaml
  env:
    # renovate: datasource=docker depName=kcov/kcov
    KCOV_IMAGE: kcov/kcov:latest@sha256:...
  ```

- **Workflow tool versions.** A plain version in any `key: value` line of a workflow, below
  a `# renovate:` comment. The version of `ludeeus/action-shellcheck` is the typical case:

  ```yaml
  with:
    # renovate: datasource=github-releases depName=koalaman/shellcheck
    version: v0.11.0
  ```

- **Makefile tool images.** A lint or scan image in a `?=` variable of the root
  `Makefile`, below a `# renovate:` comment:

  ```make
  # renovate: datasource=docker depName=hadolint/hadolint
  HADOLINT_IMAGE ?= hadolint/hadolint:v2.15.1@sha256:...
  ```

pip-compile: the header of a compiled file may use only the options Renovate knows. Compile
with `uv pip compile --generate-hashes --universal --python-version=X.Y <in>
--output-file=<txt>`. Renovate rejects `-o` and `--only-binary`; enforce binary wheels at
install time with `pip install --only-binary=:all: --require-hashes -r <txt>`.

zizmor:

- Renovate reads the `version` input of `zizmorcore/zizmor-action` as the image
  `ghcr.io/zizmorcore/zizmor`. No custom manager is needed.
- The action accepts only a plain `X.Y.Z` version, so the preset never pins it by digest.
- The action runs only the zizmor versions its own release lists. The action and the
  zizmor version come in one pull request, group `zizmor`. This rule comes after the
  `github actions` group and wins over it.

## Changes

A change here reaches every repository that extends the preset on its next Renovate run.

1. Open a pull request. CI runs `renovate-config-validator --strict` on the preset.
2. Before the merge, run a local dry run in one affected repository. See
   [CONTRIBUTING.md](CONTRIBUTING.md).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Report vulnerabilities privately, see
[SECURITY.md](SECURITY.md).

## Disclaimer

This preset is provided "as is", without warranty of any kind, as the [LICENSE](LICENSE)
states. Use it at your own risk. Sergey Grigoriev is not liable for damage from its use, as
far as the law allows. It is published free of charge, outside of any commercial offering,
with no obligation to support it. Security reports are welcome, see
[SECURITY.md](SECURITY.md).

## License

MIT, see [LICENSE](LICENSE).
