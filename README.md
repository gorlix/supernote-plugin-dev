# supernote-plugin-dev

An [Agent Skill](https://agentskills.io) for building, debugging, and extending [Supernote](https://supernote.com/) e-ink device plugins with the `sn-plugin-lib` SDK (React Native + Android). Distributed as a Claude Code plugin here, but the skill itself is written to the open, tool-agnostic [Agent Skills format](https://agentskills.io/specification) — no Claude-specific frontmatter or hooks — so it works in any Agent Skills-compatible client (Antigravity, Cursor, Gemini CLI, Codex, VS Code Copilot, and 30+ others — see the [full client list](https://agentskills.io/clients)).

It bundles hard-won, verified knowledge — API signatures, coordinate-system rules, permission gotchas, native-module pitfalls, build/deploy/debug workflows — gathered from building real Supernote plugins and cross-checked against the live [`docs.supernote.com`](https://docs.supernote.com) documentation MCP. It is **generic**: not tied to any specific plugin, safe to install in any Supernote plugin repo.

## Install (Claude Code)

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

## Install (Antigravity, or any other Agent Skills client)

The `skills/supernote-plugin-dev/` folder is a self-contained, spec-compliant Agent Skill (just `SKILL.md` + `references/`, no `.claude-plugin/` needed to read it) — copy or link it into whatever directory your tool scans for skills.

**Antigravity**: place it at `<workspace-root>/.agents/skills/supernote-plugin-dev/` (workspace-level) or `~/.gemini/config/skills/supernote-plugin-dev/` (global, all workspaces). There's no remote-install command documented for Antigravity yet, so pull the folder in explicitly, e.g. as a git submodule so it stays updatable:

```bash
git submodule add https://github.com/gorlix/supernote-plugin-dev.git .agents/skills/.vendor/supernote-plugin-dev
ln -s .vendor/supernote-plugin-dev/skills/supernote-plugin-dev .agents/skills/supernote-plugin-dev
```

(or simpler, if you don't need updates via `git submodule update`: just `git clone` and copy the `skills/supernote-plugin-dev/` folder in directly.)

**Other clients**: check [agentskills.io/clients](https://agentskills.io/clients) for your tool's specific skills directory — the same `skills/supernote-plugin-dev/` folder works everywhere, only the destination path changes.

## What's inside

- `skills/supernote-plugin-dev/SKILL.md` — entry point: architecture overview, plugin lifecycle, development workflow, critical constraints, a full API decision tree, and 40+ numbered gotchas.
- `skills/supernote-plugin-dev/references/` — deeper reference docs per topic: API quick reference, common code patterns, type definitions, i18n, floating windows, pen/EMR handling, SQLite storage, and setup/build/debug.

The live [`supernote-docs` MCP](https://docs.supernote.com/mcp) (add it with `claude mcp add --transport http --scope project supernote-docs https://docs.supernote.com/mcp`) is always the authoritative source for API signatures — this skill points to it and defers to it whenever the two disagree.

## Contributing

Found something wrong, outdated, or missing? Open an issue or a PR — this is meant to grow as more people build Supernote plugins and hit new edge cases.

## License

MIT © [Gorlix](https://github.com/gorlix)
