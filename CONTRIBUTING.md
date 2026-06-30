# Contributing to kitchen-brigade

Thanks for your interest! This is a Claude Code plugin distributed through the
`avegancafe/avegancafe-marketplace`.

## Golden rule: bump the version

**Every change bumps the version** — including docs. Update, together, in one PR:

1. `version` in [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) (semver)
2. A new entry in [`CHANGELOG.md`](CHANGELOG.md)
3. The `kitchen-brigade` entry's `version` in the marketplace repo
   (`avegancafe/avegancafe-marketplace` → `.claude-plugin/marketplace.json`)

These three values must always match.

## Local development

```bash
git clone git@github.com:avegancafe/kitchen-brigade.git
/plugin marketplace add /path/to/avegancafe-marketplace
/plugin install kitchen-brigade@avegancafe-marketplace
# restart Claude Code, then exercise /chef, /chef-implement, etc.
```

## Component rules

- **Manifests only** live in `.claude-plugin/`; agents/commands/skills live at the repo root.
- **Use `${CLAUDE_PLUGIN_ROOT}`** for all in-repo paths referenced from command/agent bodies — never `~/.claude/...` or absolute paths.
- **No hard external dependencies** (Codex/Playwright/Figma are optional).
- Keep agent frontmatter (`name`, `description`, `model`) consistent with every reference to it.

## Pull requests

- Keep changes focused; one logical change per PR.
- Validate JSON: `python3 -m json.tool .claude-plugin/plugin.json`.
- Describe what changed and why; reference the version bump.

See [`CLAUDE.md`](CLAUDE.md) for the full maintainer guide and the pattern-library table of contents.
