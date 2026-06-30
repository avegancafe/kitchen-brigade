# CLAUDE.md — kitchen-brigade

Guidance for Claude Code when working **on this repository** (developing/maintaining the plugin), not when using it.

---

## ⚠️ CRITICAL — read before any change

1. **Bump the version on EVERY change to this repo.** Edit `version` in
   [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) (semver), add a
   matching entry to [`CHANGELOG.md`](CHANGELOG.md), **and** update the
   `kitchen-brigade` entry's `version` in the marketplace repo
   (`avegancafe/avegancafe-marketplace` → `.claude-plugin/marketplace.json`).
   These three must always agree. No "trivial" exception — docs-only changes bump
   the patch version too.
2. **Paths in command bodies use `${CLAUDE_PLUGIN_ROOT}`** — never `~/.claude/...`
   or absolute paths. The chef runs *embedded*, so commands tell Claude to read
   `${CLAUDE_PLUGIN_ROOT}/agents/chef.md` (and cook/expediter/sous-chef). Keep that
   variable form or the plugin breaks on other installs.
3. **No hard external dependencies.** Codex / Playwright / Figma are optional —
   never make the brigade require them.

---

## 📚 Pattern library — `.claude/patterns/`

Accumulated, durable patterns for this repo live in
[`.claude/patterns/`](.claude/patterns/). **This section is their table of
contents — keep it in sync whenever a pattern file is added, renamed, or removed.**

| Pattern | What it covers |
|---------|----------------|
| _(none yet)_ | Add patterns via the `/learn` skill, or by hand into `.claude/patterns/<slug>.md`, then list them here. |

See [`.claude/patterns/README.md`](.claude/patterns/README.md) for the convention.

---

## What this repo is

A Claude Code **plugin** packaging a multi-agent orchestration system ("the
brigade"). Distributed via the private `avegancafe/avegancafe-marketplace` and
installed with `/plugin install kitchen-brigade@avegancafe-marketplace`.

## Layout

```
.claude-plugin/plugin.json   # Manifest (name, version, metadata). REQUIRED.
.claude/patterns/            # Durable repo patterns (see ToC above)
agents/                      # The cast: chef, cook, expediter, sous-chef (.md + frontmatter)
commands/                    # Slash commands: chef, chef-strategize, chef-implement, chef-workflow, chef-diagnose
skills/yes-chef/SKILL.md     # The "greenlight" skill that fires an agreed plan
skills/dinner-rush/SKILL.md  # Concurrency-aware posture for working alongside parallel agents
README.md                    # User-facing docs
```

`.claude-plugin/` holds **only** manifests — never put agents/commands/skills inside it.

## Editing rules

- **Agent frontmatter** (`name`, `description`, `model`, `color`, `effort`) drives
  registration and model selection. Don't rename `name:` without updating every
  reference in the commands.
- After any change, follow the version-bump rule above.

## Testing changes locally

```bash
/plugin marketplace add /Users/kyle/workspace/avegancafe-marketplace
/plugin install kitchen-brigade@avegancafe-marketplace
# restart Claude Code, then exercise /chef, /chef-implement, etc.
```

## Provenance

Extracted from Kyle's `~/.claude/` (the Juliet dotfiles). Adapted from a J2
`/muppets` brigade; consolidated 7 agents → 4.
