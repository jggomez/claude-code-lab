# Lab 07 — Plugins

**Time:** 8 min · **Theory:** [06 — Plugins](../theory/06-plugins.md)

## Goal
Package what you built (skill + agents + test hook) into a plugin and load it in a *different*, empty project.

## Steps

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
A plugin can be published through a **marketplace** (a git repo that lists plugins) and installed with `/plugin install <name>@<marketplace>`. Who in your team would benefit from this?

## Checkpoint
- [ ] `claude plugin validate` passes
- [ ] In `scratch/`, `/plugin` shows `taskboard-kit` enabled
- [ ] The namespaced skill and agents are available
- [ ] You can explain why `skills/` sits at the plugin root, not in `.claude-plugin/`

## What to observe
The `.claude/` folder in your project and a plugin are the **same pieces** in a different wrapper. Plugins add namespacing, versioning and distribution.

## If something fails
- Components not loading → they must be at the plugin root, only `plugin.json` inside `.claude-plugin/`.
- Hook doesn't run → script path must use `${CLAUDE_PLUGIN_ROOT}`; script must be executable (`chmod +x`).
- Flags differ → `claude plugin --help`.

**Next:** [Lab 08 — Wrap-up](08-wrap-up-and-challenges.md)
