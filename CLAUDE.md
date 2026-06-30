# CLAUDE.md — kitchen-brigade plugin

Guidance for Claude Code when working **on this repository** (developing/maintaining the plugin), not when using it.

## What this repo is

A Claude Code **plugin** that packages a multi-agent orchestration system ("the brigade"). It is distributed via the private `avegancafe/avegancafe-marketplace` and installed with `/plugin install kitchen-brigade@avegancafe-marketplace`.

## Layout

```
.claude-plugin/plugin.json   # Manifest (name, version, metadata). REQUIRED.
agents/                      # The cast: chef, cook, expediter, sous-chef (one .md each, with frontmatter)
commands/                    # Slash commands: chef, chef-strategize, chef-implement, chef-workflow, chef-diagnose
skills/yes-chef/SKILL.md     # The "greenlight" skill that fires an agreed plan
README.md                    # User-facing docs
```

`.claude-plugin/` holds **only** manifests — never put agents/commands/skills inside it. Components live at the repo root.

## Rules when editing

- **Paths in command bodies use `${CLAUDE_PLUGIN_ROOT}`**, not `~/.claude/...` or absolute paths. The chef runs *embedded* (the main thread becomes it), so the commands instruct Claude to read `${CLAUDE_PLUGIN_ROOT}/agents/chef.md` (and cook/expediter/sous-chef) to adopt the role. Keep that variable form so the plugin stays portable across installs.
- **Agent frontmatter** (`name`, `description`, `model`, `color`, `effort`) drives how each agent is registered and which model it runs on. Don't rename `name:` without updating every reference in the commands.
- **Bump `version` in `.claude-plugin/plugin.json`** on any meaningful change, and update the matching `version` for `kitchen-brigade` in the marketplace repo's `marketplace.json`. Use semver.
- **No hard external dependencies.** Codex / Playwright / Figma are optional — never make the brigade require them.

## Testing changes locally

```bash
/plugin marketplace add /Users/kyle/workspace/avegancafe-marketplace   # or the local plugin dir
/plugin install kitchen-brigade@avegancafe-marketplace
# restart Claude Code, then exercise /chef, /chef-implement, etc.
```

## Provenance

Extracted from Kyle's `~/.claude/` (the Juliet dotfiles). Adapted from a J2 `/muppets` brigade; consolidated 7 agents → 4.
