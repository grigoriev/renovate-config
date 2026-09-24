# renovate-config

[![CI](https://github.com/grigoriev/renovate-config/actions/workflows/ci.yml/badge.svg)](https://github.com/grigoriev/renovate-config/actions/workflows/ci.yml)
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
preset.

## What the preset does

| Setting | Effect |
|---|---|
| `config:recommended` | Renovate's recommended base, with the dependency dashboard |
| `helpers:pinGitHubActionDigests` | GitHub Actions are pinned by commit SHA, the version stays in a comment |
| `pinDigests: true` | Docker images are pinned by digest |
| `automerge`, `automergeType: pr`, `platformAutomerge: true` | Renovate opens a pull request and turns on GitHub auto-merge. GitHub merges it as soon as the required checks pass |
| `osvVulnerabilityAlerts: true` | Updates for known vulnerabilities come from the OSV database too |
| Dashboard approval | Off for every update, majors included. It also overrides an approval gate set on the Mend side |

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

- **Makefile tool images.** A lint or scan image in a `?=` variable of the root
  `Makefile`, below a `# renovate:` comment:

  ```make
  # renovate: datasource=docker depName=hadolint/hadolint
  HADOLINT_IMAGE ?= hadolint/hadolint:v2.15.1@sha256:...
  ```

zizmor:

- Renovate reads the `version` input of `zizmorcore/zizmor-action` as the image
  `ghcr.io/zizmorcore/zizmor`. No custom manager is needed.
- The action accepts only a plain `X.Y.Z` version, so the preset never pins it by digest.
- The action runs only the zizmor versions its own release lists. The action and the
  zizmor version come in one pull request, group `zizmor`.

## Changes

A change here reaches every repository that extends the preset on its next Renovate run.

1. Open a pull request. CI runs `renovate-config-validator --strict` on the preset.
2. Before the merge, run a local dry run in one affected repository. See
   [CONTRIBUTING](#contributing).

## Contributing

Conventional Commits, signed commits, squash merge. Check a change locally:

```sh
docker run --rm -v "$PWD":/work -w /work --entrypoint renovate-config-validator \
  renovate/renovate --strict --no-global default.json renovate.json
```

Dry run of a repository that uses the preset, from its committed checkout:

```sh
docker run --rm -v "$PWD":/repo -w /repo -e RENOVATE_PLATFORM=local \
  -e RENOVATE_DRY_RUN=lookup -e LOG_LEVEL=debug -e GITHUB_COM_TOKEN="$(gh auth token)" \
  renovate/renovate
```

The dry run fetches the preset from GitHub, so it tests the preset on `main`.

## Disclaimer

This preset is provided "as is", without warranty of any kind, as the LICENSE states. Use
it at your own risk. Sergey Grigoriev is not liable for damage from its use, as far as the
law allows. It is published free of charge, outside of any commercial offering, with no
obligation to support it. Security reports are welcome, see SECURITY.md.
