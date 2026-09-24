# Contributing

Issues and pull requests are welcome.

## Build and test

```sh
# validate the preset and the repository config
docker run --rm -v "$PWD":/work -w /work --entrypoint renovate-config-validator \
  renovate/renovate --strict --no-global default.json renovate.json
```

Dry run of a repository that uses the preset, from its committed checkout:

```sh
docker run --rm -v "$PWD":/repo -w /repo -e RENOVATE_PLATFORM=local \
  -e RENOVATE_DRY_RUN=lookup -e LOG_LEVEL=debug -e GITHUB_COM_TOKEN="$(gh auth token)" \
  renovate/renovate
```

The dry run fetches the preset from GitHub, so it tests the preset on `main`. To test a
change before the merge, copy the content of `default.json` into the `renovate.json` of the
throwaway checkout in place of the `extends` entry.

CI runs actionlint, zizmor and `renovate-config-validator --strict` for every pull request.

## Pull requests

1. Branch from the default branch as `type/description`, for example `fix/zizmor-group`.
2. Keep one change per pull request. Show the dry run result of an affected repository in
   the pull request.
3. Write commit messages as [Conventional Commits](https://www.conventionalcommits.org/) without a
   scope: `feat: ...`, `fix: ...`, `docs: ...`, `refactor: ...`, `test: ...`, `build: ...`,
   `ci: ...`, `chore: ...`.
4. Sign your commits. The default branch accepts verified signatures only.
5. Add an entry under `## [Unreleased]` in `CHANGELOG.md`, written for users. Update the
   README when behavior changes.

Pull requests are squash-merged once all required checks are green.

## Releases

The preset has no releases. Renovate reads it from `main`, so a merge reaches every
repository that extends it on its next run.
