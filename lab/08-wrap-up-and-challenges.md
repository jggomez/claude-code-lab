# Lab 08 — Wrap-up and Challenges

**Time:** 4 min (+ homework)

## The harness map (recap)
Fill in from memory, then check against the lab:

| Piece | What you built | Lab |
|---|---|---|
| CLAUDE.md | Project memory with spec/stack imports | 01 |
| Plan mode | Plan before code | 01 |
| Permissions | allow/deny rules | 02 |
| Hooks | Tests after every edit | 02 |
| Skills | `add-user-story` | 03 |
| MCP | Playwright browser | 04 |
| Loops | Verification, `/loop`, headless script | 05 |
| Subagents | `code-reviewer`, `qa-tester` | 06 |
| Plugins | `taskboard-kit` | 07 |

```
Better results = better harness:  context + rules + checks + tools + specialists
```

## Habits to keep
1. **Plan first** for anything non-trivial.
2. **Give Claude a way to verify** (tests, URL, browser).
3. **One task per session**; `/clear` between tasks.
4. **Enforce with hooks**, advise with CLAUDE.md.
5. **Least privilege**: narrow permissions, read-only reviewers.
6. **Review diffs**. You own the result.

## Challenges
1. **HU6** — Implement priority + pending counter using only your skill, plan mode and the subagents.
2. **Block secrets** — Write a `PreToolUse` hook that blocks edits to `.env` (exit code 2 with a message).
3. **Commit skill** — Create a `/commit` skill that writes Conventional Commit messages from `git diff`.
4. **Parallel review** — Add a `security-reviewer` subagent and run it in parallel with `code-reviewer`.
5. **Persistence** — Ask Claude (plan mode first) to persist tasks to `data.json`; update `spec.md` and `stack.md` first via the skill.
6. **Headless in CI** — Write a script that runs `claude -p` with `--output-format json` to produce a review report.

## Troubleshooting quick list
| Symptom | Try |
|---|---|
| Claude ignores a convention | Put it in CLAUDE.md; if it *must* hold, make it a hook |
| Responses get worse over time | `/context`, then `/compact` or `/clear` |
| Too many permission prompts | Add safe commands to `permissions.allow` |
| Skill/agent not found | Check path + frontmatter, restart Claude |
| MCP server missing | `/mcp`, `claude mcp list`, restart |

## Feedback
What was the most useful piece? What would you automate first in your own project?

**Back to:** [README](../README.md)
