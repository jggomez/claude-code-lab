# 02 — The Harness

**Read time:** ~5 min · **Used in:** [lab 02](../lab/02-harness-hooks-permissions.md) and every lab after it

## Model vs. harness
The **model** (Claude) only produces text. It cannot read your disk, run a command or remember yesterday.

The **harness** is everything around it that turns text into action. Claude Code *is* a harness:

```
┌─────────────────────────── HARNESS (Claude Code) ───────────────────────────┐
│                                                                             │
│  Context        CLAUDE.md · conversation · files it read                    │
│  Tools          Read · Edit · Write · Bash · Grep · WebFetch ...            │
│  Permissions    allow / ask / deny rules, permission modes                  │
│  Hooks          your scripts that run on events (before/after a tool)       │
│  Skills         reusable instructions loaded on demand                      │
│  MCP servers    extra tools from outside (browser, DBs, APIs)               │
│  Subagents      specialist workers with their own context                   │
│  Plugins        a bundle of all of the above, shareable                     │
│                                                                             │
│                       ┌─────────────────┐                                   │
│                       │  MODEL (Claude) │                                   │
│                       └─────────────────┘                                   │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Key idea:** you get better results mostly by improving the harness (context, rules, checks, tools), not by writing cleverer prompts.

## The pieces and when to use each

| Piece | Where it lives | Who triggers it | Use it for |
|---|---|---|---|
| **CLAUDE.md** | `./CLAUDE.md` (project), `~/.claude/CLAUDE.md` (personal) | Loaded every session | Facts Claude must always know: stack, commands, conventions |
| **Settings** | `.claude/settings.json` (shared), `.claude/settings.local.json` (yours), `~/.claude/settings.json` | The harness | Permissions, hooks, env vars, model |
| **Permissions** | `permissions` in settings | The harness, on every tool call | Safe auto-approvals and hard denials |
| **Hooks** | `hooks` in settings | **Events** — deterministic, always run | Things that must *always* happen: run tests, format, block dangerous commands |
| **Skills** | `.claude/skills/<name>/SKILL.md` | Claude (by description) or you (`/name`) | Repeatable procedures and templates |
| **MCP** | `.mcp.json` / `claude mcp add` | Claude calls the tools | Capabilities Claude lacks: browser, issue tracker, DB |
| **Subagents** | `.claude/agents/<name>.md` | Claude delegates, or you `@agent-name` | Isolated, focused work (review, research, testing) |
| **Plugins** | A folder with `.claude-plugin/plugin.json` | You install it | Distributing a set of the above |

## Instructions vs. enforcement
This distinction matters:

- **CLAUDE.md, skills, subagent prompts** are *instructions*. Claude usually follows them, but it is a model — it can forget.
- **Hooks and permissions** are *enforcement*. Code in the harness runs regardless of what the model decides.

> "Please run the tests after editing" → CLAUDE.md. "Tests **must** run after every edit" → hook.

## Settings scopes (highest priority first)
1. Managed (organization) settings
2. Command-line flags
3. `.claude/settings.local.json` — yours, not committed
4. `.claude/settings.json` — project, committed and shared
5. `~/.claude/settings.json` — all your projects

## Permission rules: syntax
```json
{
  "permissions": {
    "allow": ["Bash(npm test)", "Bash(npm run *)", "Edit"],
    "deny":  ["Bash(rm -rf *)", "Read(./.env)"]
  }
}
```
`deny` wins over `allow`. Manage interactively with `/permissions`.

## Hooks: the shape
A hook is a command that receives JSON on stdin describing the event.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [{ "type": "command", "command": "bash .claude/hooks/run-tests.sh" }]
      }
    ]
  }
}
```
Common events: `SessionStart`, `UserPromptSubmit`, `PreToolUse` (can block), `PostToolUse`, `Stop`.
Exit code `0` = fine · `2` = blocking feedback: stderr is shown to Claude · other = non-blocking error.

## Context cost of each piece
- CLAUDE.md: always in context → keep it short.
- Skills: only the *description* is always loaded; the body loads when used.
- MCP: tool definitions take context → add only what you need.
- Subagents: work in their *own* context and return only a summary → great for keeping the main context clean.

## Check yourself
- Which pieces are instructions and which are enforcement?
- You want tests to run after *every* edit, no exceptions. Which piece?
- Why do subagents help with context?

**Next:** [03 — Skills and MCP](03-skills-and-mcp.md)
