# 03 — Skills and MCP

**Read time:** ~4 min · **Used in:** [lab 03](../lab/03-skills.md), [lab 04](../lab/04-mcp.md)

## Skills

A **skill** is a folder with a `SKILL.md` — a reusable playbook. Think "a recipe Claude can pick up when it needs it".

```
.claude/skills/add-user-story/
└── SKILL.md          # required
    (templates, scripts, examples — optional extra files)
```

### SKILL.md anatomy
```markdown
---
name: add-user-story
description: Adds a new user story to spec.md using the project template. Use when the user asks to add, write or define a user story or requirement.
---

# Instructions
1. Read spec.md and find the next HU number.
2. Append a story using the template below...
```

- `name` → becomes the slash command `/add-user-story`.
- `description` → how Claude decides to load it automatically. **Write it as "what it does + when to use it".**
- Body → loaded only when the skill is used (progressive disclosure: cheap until needed).

Useful optional frontmatter: `disable-model-invocation: true` (only you can run it, e.g. for deploys), `allowed-tools` (pre-approve tools while the skill runs). Arguments typed after the command are available as `$ARGUMENTS`.

### Two ways to trigger
- **Automatic** — you say "add a story for X", Claude matches the description and loads the skill.
- **Manual** — you type `/add-user-story Export tasks as CSV`.

Skills live in `.claude/skills/` (project, shared with the team) or `~/.claude/skills/` (personal).

### Skill vs. CLAUDE.md vs. subagent

| Need | Use |
|---|---|
| A fact that is always true | CLAUDE.md |
| A procedure used sometimes | **Skill** |
| Work that needs its own context / restricted tools | Subagent |

## MCP — Model Context Protocol

An open standard that lets Claude Code talk to **external tools**. An MCP server exposes tools (and sometimes resources/prompts); the harness adds them next to Read, Edit, Bash.

```
Claude Code ──(MCP)──► Playwright server ──► real browser
                  ├───► GitHub server     ──► issues, PRs
                  └───► Database server   ──► queries
```

### Adding a server
```bash
# stdio server (runs locally as a subprocess). Note the `--` separator.
claude mcp add playwright -- npx @playwright/mcp@latest

# manage
claude mcp list
claude mcp get playwright
claude mcp remove playwright
```
Inside a session, `/mcp` shows server status.

### Scopes
| Scope | Flag | Stored in | Shared? |
|---|---|---|---|
| local (default) | `--scope local` | your user config, per project | no |
| project | `--scope project` | `.mcp.json` in the repo | yes (committed) |
| user | `--scope user` | your user config | across all your projects |

### Transports
`stdio` (local process), `http` (remote server), `sse` (legacy remote). Remote servers may need authentication via `/mcp`.

### Safety
- An MCP server runs code or reaches services **as you**. Only add servers you trust.
- MCP tools go through the permission system; approve them deliberately.
- Each server's tool list consumes context — don't install ten "just in case".

> Syntax evolves quickly. If a command here fails, run `claude mcp --help` and check the official docs.

## Skill or MCP?
- Claude **knows how** but needs a procedure → skill.
- Claude **can't reach** something (a browser, an API) → MCP.
- They compose: a skill can say "use the browser tools to verify the page".

## Check yourself
- What does `description` do in a SKILL.md?
- When is a skill's body loaded?
- Why is the `--` needed in `claude mcp add`?

**Next:** [04 — Loops](04-loops.md)
