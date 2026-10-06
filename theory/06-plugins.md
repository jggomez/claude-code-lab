# 06 — Plugins

**Read time:** ~3 min · **Used in:** [lab 07](../lab/07-plugins.md)

A **plugin** packages harness pieces — skills, subagents, hooks, MCP servers — into one shareable folder. Instead of copying files between projects, you install the plugin.

```
taskboard-kit/
├── .claude-plugin/
│   └── plugin.json        # manifest (only this goes in .claude-plugin/)
├── skills/
│   └── add-user-story/SKILL.md
├── agents/
│   ├── code-reviewer.md
│   └── qa-tester.md
├── hooks/
│   └── hooks.json         # same shape as the "hooks" key in settings.json
└── .mcp.json              # optional MCP servers
```

> Common mistake: putting `skills/` or `agents/` *inside* `.claude-plugin/`. Only `plugin.json` goes there; everything else sits at the plugin root.

## Manifest
```json
{
  "name": "taskboard-kit",
  "description": "Skills, agents and hooks for the TaskBoard lab",
  "version": "1.0.0",
  "author": { "name": "Your Name" }
}
```

## Namespacing
Plugin components are prefixed with the plugin name to avoid collisions:
- Skill → `/taskboard-kit:add-user-story`
- Agent → `taskboard-kit:code-reviewer`

## Using a plugin
**Try locally (one session, nothing installed):**
```bash
claude --plugin-dir ./taskboard-kit
```
After editing files, run `/reload-plugins`.

**Install from a marketplace:**
```
/plugin                       # browse, install, enable/disable, see errors
/plugin install name@marketplace
```
A **marketplace** is just a catalog (a git repo with a marketplace file) listing plugins. Teams use a private one to distribute standard tooling.

**Validate before sharing:**
```bash
claude plugin validate ./taskboard-kit
```

## Hooks inside plugins
Use `${CLAUDE_PLUGIN_ROOT}` to refer to files inside the plugin, since the plugin may be installed anywhere:
```json
{ "type": "command", "command": "bash ${CLAUDE_PLUGIN_ROOT}/hooks/run-tests.sh" }
```

## When to make a plugin
- You copy the same `.claude/` files into a second project.
- A team needs one standard setup.
- You want to version and update tooling centrally.

Not worth it for a single project — plain `.claude/` files are simpler.

## The whole picture

```
CLAUDE.md ──► what Claude must know
settings  ──► permissions + hooks (enforcement)
skills    ──► how to do repeatable things
MCP       ──► new capabilities
subagents ──► specialists with isolated context
loops     ──► repeat until verified / on a schedule
plugins   ──► ship all of it
```

## Check yourself
- What is the only file that lives inside `.claude-plugin/`?
- Why use `${CLAUDE_PLUGIN_ROOT}` in a plugin hook?
- When is a plugin overkill?

**Next:** [Glossary & cheatsheet](glossary-and-cheatsheet.md)
