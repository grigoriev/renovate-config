# Changelog

All notable changes to this preset are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/). The preset has no releases:
Renovate reads it from `main`.

## [Unreleased]

### Added
- One pull request per repository for minor, patch and digest updates of GitHub Actions,
  group `github actions`. The `zizmor` group stays separate.
- A custom manager for plain tool versions in workflows below a `# renovate:` comment, for
  example the ShellCheck version of `ludeeus/action-shellcheck`.
- The `pip-compile` manager covers every `.github/requirements/*.txt` file.
- OpenSSF Scorecard workflow and badge, CONTRIBUTING.md and this changelog.

### Changed
- Commits and pull request titles are Conventional Commits without a scope:
  `chore: update ...` instead of `chore(deps): update ...`.
- No hourly limit for pull requests, and up to 20 open at a time. The limits of
  `config:recommended` let action versions drift apart between repositories.
- CI cancels an older run of the same pull request, and every job has a timeout.
