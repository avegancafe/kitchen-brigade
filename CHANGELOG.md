# Changelog

All notable changes to this project are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres
to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-07-20

### Added
- Session-id self-assignment for shared project todos. When the brigade — or a session holding the `dinner-rush` posture — works a task from a projects-plugin `_projects/YYYY-MM-DD--<name>/todo.md`, it claims the task by adding `[assignee:<readable-id>]` to the line before starting (id from the `session-ids` plugin's `session-id` skill), skips tasks already claimed by another session, and flips `- [ ]` → `- [x]` on verified completion while keeping priority @-mentions and all assignee links intact. Optional dependency: degrades gracefully when `session-ids` isn't installed. Touches `skills/dinner-rush/SKILL.md` and `agents/chef.md`.

## [1.0.3] - 2026-06-30

### Changed
- Switched repo documentation to first person ("my"/"I") throughout — `CLAUDE.md` provenance note. Contents unchanged.

## [1.0.2] - 2026-06-30

### Added
- `dinner-rush` skill — moved in from the `avegancafe` plugin. A concurrency-aware posture that fits the brigade's parallel-agent model (cooks running on non-overlapping lanes).

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
