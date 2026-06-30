# Security Policy

## Supported Versions

The latest released version is supported. See [`CHANGELOG.md`](CHANGELOG.md) for releases.

## Reporting a Vulnerability

This plugin runs as part of Claude Code and can execute commands and hooks on your
machine. If you discover a security issue — for example a hook or command that
could be coerced into running unintended code — please report it privately:

- Open a [GitHub security advisory](https://github.com/avegancafe/kitchen-brigade/security/advisories/new), or
- Contact the maintainer directly rather than filing a public issue.

Please do not disclose the issue publicly until it has been addressed.

## Scope notes

- The brigade's `cook` agent can write files and run tests; review what you
  install and run, especially in repos with sensitive credentials.
- Commands and agents reference paths via `${CLAUDE_PLUGIN_ROOT}`; report any path
  handling that could escape the plugin root or execute attacker-controlled input.
