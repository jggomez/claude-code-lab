# 05 — Custom Agents (Subagents)

**Read time:** ~4 min · **Used in:** [lab 06](../lab/06-custom-agents.md)

A **subagent** is a specialist Claude with its own:
- **system prompt** (its role and rules),
- **tool allowlist** (what it may do),
- **context window** (isolated from your main conversation).

The main agent hands it a task; the subagent works and returns **only a summary**. The noise (files read, dead ends) never pollutes your main context.

```
main session ──delegates──► code-reviewer  (reads files, greps, thinks)
      ▲                          │
      └────── short summary ◄────┘
```

## Why use them
1. **Context hygiene** — big explorations stay out of the main window.
2. **Least privilege** — a reviewer that cannot edit can't "helpfully" change your code.
3. **Consistency** — the same role and checklist every time.
4. **Parallelism** — several can work at once.

## File format
`.claude/agents/<name>.md` (project) or `~/.claude/agents/<name>.md` (personal):

```markdown
---
name: code-reviewer
description: Reviews code for bugs, security and readability. Use proactively after code changes.
tools: Read, Grep, Glob
model: sonnet
---

You are a senior code reviewer for a small Node.js + vanilla JS project.

When invoked:
1. Run through the files that changed (or those you are pointed at).
2. Check against spec.md acceptance criteria and CLAUDE.md conventions.
3. Report findings as: **Blocking / Should fix / Nit**, each with file and line.

Do not modify files. Be concise.
```

Frontmatter you will use most:
| Field | Meaning |
|---|---|
| `name` | Identifier |
| `description` | **When to delegate** — Claude reads this to decide. Write "Use proactively when…" for auto-delegation |
| `tools` | Allowlist (omit = inherits all). Read-only agent: `Read, Grep, Glob` |
| `model` | `sonnet`, `opus`, `haiku` or `inherit` |

More fields exist (e.g. `maxTurns`, `permissionMode`, `skills`, `isolation`) — see `/agents` and the docs.

## Creating and invoking
- **Create:** type `/agents` and use the wizard, or write the file by hand.
- **Automatic:** Claude delegates when the task matches the `description`.
- **Explicit:** *"Use the code-reviewer subagent to review `src/app.js`"* or `@agent-code-reviewer review src/app.js`.
- **Whole session as that agent:** `claude --agent code-reviewer`.

## Designing a good one
- **One job.** "Reviews code", not "reviews, fixes and deploys".
- **Minimal tools.** Reviewer/researcher: read-only. Tester: Read + Bash (+ browser MCP).
- **Clear output format** — you want a summary you can act on.
- **Sharp description** — vague descriptions are never delegated to.

## Subagent vs. skill vs. plain prompt

| | Own context | Own tools | Best for |
|---|---|---|---|
| Plain prompt | no | no | One-off work |
| Skill | no (runs in main) | optional | Reusable procedure |
| **Subagent** | **yes** | **yes** | Isolated, role-based work |

Subagents can't spawn their own subagents in the usual setup; the main session orchestrates.

## Check yourself
- What returns to the main conversation when a subagent finishes?
- Why give the reviewer only `Read, Grep, Glob`?
- Which field decides whether Claude delegates automatically?

**Next:** [06 — Plugins](06-plugins.md)
