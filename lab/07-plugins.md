# Lab 07 — Plugins

**Time:** 8 min (Part A 4 + Part B 4; Part B can be an instructor demo) · **Theory:** [06 — Plugins](../theory/06-plugins.md)

## Goal
Two things: **install** a plugin made by someone else (the everyday case), then **build** your own from what you created in the previous labs and load it in a different project.

## Part A — Install a community plugin (4 min)

A plugin comes from a **marketplace** (a catalog of plugins). Anthropic's official marketplace, `claude-plugins-official`, is added for you the first time you run Claude Code interactively, so you can install from it right away.

### A1. Browse
Inside `claude`:
```
/plugin
```
The **Discover** tab lists plugins from your marketplaces. Type to search, press **Enter** on one to read its details.

### A2. Install one
Example: `commit-commands`, which adds commit / push / PR commands.
```
/plugin install commit-commands@claude-plugins-official
```
This opens the plugin's details so you can **review it before installing**:
- **Will install** — the commands, agents, skills, hooks and MCP servers it adds.
- **Context cost** — tokens it adds to every turn (for official plugins).

Then choose a **scope**:
| Scope | Meaning |
|---|---|
| User | You, in every project on this machine |
| Project | Everyone on this repo (recorded in `.claude/settings.json`) |
| Local | You, in this repo only |

For the lab pick **local** (or user).

### A3. Confirm and use it
Read the install summary (if it says `Run /reload-plugins to apply`, do that). Type `/` — you should see `/commit-commands:commit`. Try it on your lab changes:
```
/commit-commands:commit
```

### A4. Add a marketplace from the community (optional)
Plugins from other publishers need their marketplace added once. A marketplace source is a GitHub `owner/repo`, a git URL, or a local path:
```
/plugin marketplace add owner/repo
/plugin install some-plugin@marketplace-name
```
or both at once:
```
/plugin install some-plugin --marketplace owner/repo
```
Look for plugins at https://claude.com/marketplace, then paste the install command shown there.

### A5. Manage
```
/plugin                                  # Installed tab: enable, disable, update, uninstall
claude plugin list                       # from your shell
claude plugin uninstall commit-commands@claude-plugins-official
```

### Trust check — a plugin is code
A plugin can run **hooks** (commands on events) and **MCP servers** with your permissions. Before installing from a marketplace you don't know:
- Read the **Will install** list; look for hooks and MCP servers.
- Prefer the official marketplace and well-known publishers; check the source repo.
- Remove what you don't use (extra plugins also cost context).

### Checkpoint for Part A
- [ ] You browsed the Discover tab
- [ ] `commit-commands` is installed and `/commit-commands:commit` appears
- [ ] You can say what each scope means
- [ ] You know what to check before installing a plugin you don't know

## Part B — Build your own plugin (4 min, instructor demo if short on time)

Package what you built in Labs 02, 03 and 06 so it can be reused.


### 1. Build the plugin skeleton
From the folder **above** `taskboard/`:
```bash
mkdir -p taskboard-kit/.claude-plugin taskboard-kit/skills taskboard-kit/agents taskboard-kit/hooks
```

### 2. Manifest
`taskboard-kit/.claude-plugin/plugin.json`:
```json
{
  "name": "taskboard-kit",
  "description": "User-story skill, reviewer/QA agents and a test hook for the TaskBoard lab",
  "version": "1.0.0",
  "author": { "name": "Your Name" }
}
```

### 3. Copy your pieces
```bash
cp -r taskboard/.claude/skills/add-user-story taskboard-kit/skills/
cp taskboard/.claude/agents/*.md              taskboard-kit/agents/
cp taskboard/.claude/hooks/run-tests.sh       taskboard-kit/hooks/
```

### 4. Hooks file
`taskboard-kit/hooks/hooks.json`:
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "bash ${CLAUDE_PLUGIN_ROOT}/hooks/run-tests.sh" }
        ]
      }
    ]
  }
}
```
> Some versions expect the hooks block inline in `plugin.json` instead. If `/hooks` doesn't show it, check `claude plugin validate` and the docs.

### 5. Validate
```bash
claude plugin validate ./taskboard-kit
```
Fix anything it reports.

### 6. Try it in a fresh project
```bash
mkdir scratch && cd scratch
claude --plugin-dir ../taskboard-kit
```
Inside Claude:
```
/plugin
/agents
```
Then:
```
/taskboard-kit:add-user-story A dark mode toggle
```
(The skill expects `docs/spec.md`; ask Claude to create a minimal one first if it complains.)

### 7. Edit and reload
Change a line in the reviewer's prompt inside `taskboard-kit/agents/code-reviewer.md`, then:
```
/reload-plugins
```

### 8. Distribution (discussion, no commands)
A plugin can be published through your own **marketplace** (a git repo with `.claude-plugin/marketplace.json` that lists plugins) and installed by teammates with `/plugin marketplace add <owner/repo>` + `/plugin install <name>@<marketplace>`. Who in your team would benefit from this?

## Checkpoint
- [ ] (Part B) `claude plugin validate` passes
- [ ] (Part B) In `scratch/`, `/plugin` shows `taskboard-kit` enabled
- [ ] The namespaced skill and agents are available
- [ ] You can explain why `skills/` sits at the plugin root, not in `.claude-plugin/`

## What to observe
The `.claude/` folder in your project and a plugin are the **same pieces** in a different wrapper. Plugins add namespacing, versioning and distribution.

## If something fails
- Components not loading → they must be at the plugin root, only `plugin.json` inside `.claude-plugin/`.
- Hook doesn't run → script path must use `${CLAUDE_PLUGIN_ROOT}`; script must be executable (`chmod +x`).
- Flags differ → `claude plugin --help`.

**Next:** [Lab 08 — Wrap-up](08-wrap-up-and-challenges.md)
