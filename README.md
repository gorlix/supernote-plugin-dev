# supernote-plugin-dev

A Claude Code skill/plugin for building, debugging, and extending [Supernote](https://supernote.com/) e-ink device plugins with the `sn-plugin-lib` SDK (React Native + Android).

It bundles hard-won, verified knowledge — API signatures, coordinate-system rules, permission gotchas, native-module pitfalls, build/deploy/debug workflows — gathered from building real Supernote plugins and cross-checked against the live [`docs.supernote.com`](https://docs.supernote.com) documentation MCP. It is **generic**: not tied to any specific plugin, safe to install in any Supernote plugin repo.

## Install

Inside a Supernote plugin project, in an interactive Claude Code session:

```
/plugin marketplace add gorlix/supernote-plugin-dev
/plugin install supernote-plugin-dev@supernote-plugin-dev --scope project
```

Use `--scope project` (not the default `user`/global scope) so the skill only applies to Supernote plugin repos, not every project on your machine. `--scope project` writes the install to `.claude/settings.json`, so it's shared with anyone who clones the repo.

## Update

When this repo publishes a new version:

```
/plugin update supernote-plugin-dev@supernote-plugin-dev
```

Updates are explicit, not automatic.

## What's inside

- `skills/supernote-plugin-dev/SKILL.md` — entry point: architecture overview, plugin lifecycle, development workflow, critical constraints, a full API decision tree, and 40+ numbered gotchas.
- `skills/supernote-plugin-dev/references/` — deeper reference docs per topic: API quick reference, common code patterns, type definitions, i18n, floating windows, pen/EMR handling, SQLite storage, and setup/build/debug.

The live [`supernote-docs` MCP](https://docs.supernote.com/mcp) (add it with `claude mcp add --transport http --scope project supernote-docs https://docs.supernote.com/mcp`) is always the authoritative source for API signatures — this skill points to it and defers to it whenever the two disagree.

## Contributing

Found something wrong, outdated, or missing? Open an issue or a PR — this is meant to grow as more people build Supernote plugins and hit new edge cases.

## License

MIT © [Gorlix](https://github.com/gorlix)
