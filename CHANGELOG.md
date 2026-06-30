# Changelog

All notable changes to this project are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres
to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.1] - 2026-06-30

### Added
- Standard OSS community files: `CONTRIBUTING.md`, `CHANGELOG.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`.
- `.claude/patterns/` pattern library with a table of contents maintained in `CLAUDE.md`.

### Changed
- `CLAUDE.md` rewritten to lead with critical rules (version bumping) and serve as the pattern-library ToC.

## [1.0.0] - 2026-06-30

### Added
- Initial release. Multi-agent "brigade" orchestration: `chef`, `cook`, `expediter`, `sous-chef` agents.
- Commands: `/chef` (router), `/chef-strategize`, `/chef-implement`, `/chef-workflow`, `/chef-diagnose`.
- `yes-chef` skill — fires an already-agreed plan.
