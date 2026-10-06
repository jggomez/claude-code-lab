# 01 — Fundamentals

**Read time:** ~5 min · **Used in:** [lab 00](../lab/00-setup.md), [lab 01](../lab/01-bases.md)

## What is Claude Code?
Claude Code is an **agent that lives in your terminal** (also available in IDEs, desktop and web). You describe a goal in natural language; it reads your code, edits files, runs commands and checks its own work.

It is not autocomplete. It is a loop.

## The agentic loop

```
        ┌──────────────────────────────────────────┐
        │                                          │
  you ──► prompt ──► MODEL thinks ──► picks a TOOL ─┤
                        ▲              (Read, Edit,  │
                        │               Bash, ...)   │
                        │                   │        │
                        └──── result ◄──────┘        │
                                                     │
              repeats until the goal is met ─────────┘──► answer
```

1. **Gather context** — read files, grep, run commands.
2. **Take action** — edit files, run tests, call tools.
3. **Verify** — run tests, look at output, fix and repeat.

You can interrupt at any time (`Esc`) and steer it.

## Context window
Everything the model "knows" in a session — your prompts, files it read, tool output — lives in a limited **context window**. When it fills up, quality drops.

| Command | What it does |
|---|---|
| `/context` | Shows how full the context is |
| `/compact` | Summarizes the conversation to free space |
| `/clear` | Starts fresh (do this between unrelated tasks) |
| `/rewind` | Goes back to an earlier point (conversation and/or code) |

**Rule of thumb:** one task per session, `/clear` between tasks.

## Permissions
By default Claude **asks before** doing anything that changes things (editing files, running shell commands). You choose per action:
- Allow once
- Allow always (for this pattern)
- Deny

Permission modes (cycle with `Shift+Tab`):
- **Default** — asks for edits and commands.
- **Accept edits** — auto-approves file edits, still asks for commands.
- **Plan mode** — read-only; Claude researches and proposes a plan, changes nothing.

## Plan mode: think before you build
For anything non-trivial, start in plan mode. Claude explores, writes a plan, and you approve or correct it **before** any file is touched. Cheap to fix a plan, expensive to fix 20 edited files.

## Talking to Claude well
- Be specific: *"Add a DELETE endpoint in `src/app.js` returning 404 for unknown ids"* beats *"add delete"*.
- Point at files with `@path/to/file`.
- Give it a way to verify: tests, a command, a URL. **This is the single highest-leverage habit.**
- Reference the spec: *"Implement HU2 from `spec.md`."*

## CLAUDE.md — project memory
A markdown file at the project root that Claude reads at the start of **every** session: stack, commands, conventions. Generate a first draft with `/init`, then edit it by hand. Keep it short.

## Essential commands

| Command | Purpose |
|---|---|
| `/init` | Generate a `CLAUDE.md` for the project |
| `/help` | List commands |
| `/clear`, `/compact`, `/context`, `/rewind` | Context management (see above) |
| `/model` | Choose the model |
| `@file` | Attach a file to your prompt |
| `!command` | Run a shell command directly from the prompt |
| `Shift+Tab` | Cycle permission modes (plan mode) |
| `Esc` | Interrupt |

## Check yourself
- What are the three phases of the agentic loop?
- Why is "give it a way to verify" so important?
- When would you use plan mode?

**Next:** [02 — The Harness](02-harness.md)
