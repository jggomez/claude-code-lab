# Glossary & Cheatsheet

## Glossary
| Term | Meaning |
|---|---|
| **Agentic loop** | Gather context → act → verify, repeated |
| **Harness** | Everything around the model: tools, context, permissions, hooks, skills, MCP, subagents, plugins |
| **Context window** | The model's working memory for the session |
| **CLAUDE.md** | Project memory file loaded every session |
| **Plan mode** | Read-only mode: research and propose, don't change |
| **Hook** | A command the harness runs on an event; deterministic |
| **Skill** | Folder with `SKILL.md`; reusable procedure loaded on demand |
| **MCP** | Model Context Protocol; connects Claude to external tools |
| **Subagent** | Specialist with its own prompt, tools and context |
| **Plugin** | Shareable bundle of skills/agents/hooks/MCP |
| **Headless** | Non-interactive run via `claude -p` |

## Slash commands
| Command | Purpose |
|---|---|
| `/init` | Generate CLAUDE.md |
| `/plan` | Enter plan mode |
| `/clear` · `/compact` · `/context` · `/rewind` | Manage context / roll back |
| `/memory` | Edit memory files |
| `/permissions` | View/edit permission rules |
| `/hooks` | View configured hooks |
| `/agents` | Manage subagents |
| `/mcp` | MCP server status |
| `/plugin` · `/reload-plugins` | Manage plugins |
| `/loop <interval> <prompt>` | Recurring prompt |
| `/diff` · `/model` · `/config` · `/help` | Utilities |

## Keys and prefixes
| Input | Effect |
|---|---|
| `Shift+Tab` | Cycle permission mode (incl. plan) |
| `Esc` | Interrupt |
| `@path` | Attach file |
| `@agent-name` | Force a subagent |
| `!cmd` | Run shell command |

## File map
```
CLAUDE.md                         project memory
.claude/settings.json             permissions + hooks (shared)
.claude/settings.local.json       your overrides (not committed)
.claude/agents/<name>.md          subagents
.claude/skills/<name>/SKILL.md    skills
.claude/hooks/*.sh                hook scripts
.mcp.json                         project MCP servers
<plugin>/.claude-plugin/plugin.json   plugin manifest
```

## CLI
```bash
claude                      # interactive
claude -p "prompt"          # headless
claude -p ... --max-turns 8 --allowedTools "Read,Edit,Bash(npm test)"
claude --plugin-dir ./dir   # load a plugin for this session
claude mcp add <name> -- <command...>
claude mcp list
```

> Claude Code changes fast. When something here doesn't match, trust `/help`, `claude --help` and the official docs.
