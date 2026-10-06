# Lab 06 — Custom Agents (Subagents)

**Time:** 12 min · **Theory:** [05 — Custom Agents](../theory/05-custom-agents.md)

## Goal
Create two specialists — a read-only **code reviewer** and a **QA tester** — and see delegation and context isolation in action.

## Steps

### 1. Create the reviewer
Create `.claude/agents/code-reviewer.md`:
```markdown
---
name: code-reviewer
description: Reviews TaskBoard code for bugs, spec compliance and readability. Use proactively after implementing or changing a user story.
tools: Read, Grep, Glob
model: sonnet
---

You are a senior reviewer for a small Node.js + vanilla JS app.

When invoked:
1. Read docs/spec.md, docs/stack.md and CLAUDE.md.
2. Review the code relevant to the request (or the whole project if unspecified).
3. Check: acceptance criteria met, error handling, test coverage, stack/convention violations, security basics (input validation, XSS in the DOM code).

Report as:
- **Blocking** — must fix
- **Should fix**
- **Nit**
Each item: file:line, problem, suggested fix. Be concise. Never modify files.
```

### 2. Create the QA tester
Create `.claude/agents/qa-tester.md`:
```markdown
---
name: qa-tester
description: Tests the running TaskBoard app against the acceptance criteria in docs/spec.md. Use when asked to QA, verify or smoke-test a user story.
tools: Read, Grep, Glob, Bash
model: sonnet
---

You are a QA engineer. You verify behavior, you do not fix code.

When invoked:
1. Read the acceptance criteria of the user story you are asked to test in docs/spec.md.
2. Exercise the API with curl against http://localhost:3000 (start the app only if it is not running).
3. If browser tools are available, also check the UI.
4. Report a table: criterion | PASS/FAIL | evidence.
Never edit source files.
```
> Tip: omit `tools` to inherit everything (including MCP tools) — or list the exact MCP tools you want it to have.

### 3. Verify they exist
```
/agents
```
Both should be listed. (Restart `claude` if not.)

### 4. Explicit invocation
```
Use the code-reviewer subagent to review the HU5 implementation.
```
or force it:
```
@agent-code-reviewer review src/ and public/
```
Notice: the main conversation only receives the **summary**. Run `/context` — the reviewer's file reads didn't fill your window.

### 5. Automatic delegation
```
I just finished HU5. Check that it meets the spec.
```
Watch whether Claude picks `code-reviewer` or `qa-tester` on its own. If not, sharpen the `description` fields.

### 6. Fix findings
```
Fix the "Blocking" items from the review. Keep the tests green.
```
Then run the QA tester:
```
Use the qa-tester subagent to verify HU1–HU5.
```

### 7. Prove least privilege
```
@agent-code-reviewer add a comment to src/app.js
```
It can't — it has no Edit tool. That's the point.

### 8. Commit
```
!git add -A && git commit -m "Review fixes + subagents"
```

## Checkpoint
- [ ] `/agents` lists `code-reviewer` and `qa-tester`
- [ ] You received a structured review (Blocking / Should fix / Nit)
- [ ] QA produced a PASS/FAIL table with evidence
- [ ] The reviewer refused/failed to edit files

## What to observe
Subagents = **role + tools + isolated context**. Your main session stayed small.

## Stretch
Run both agents in parallel: `Run code-reviewer and qa-tester in parallel on HU5 and combine their reports.`

## If something fails
- Agent not found → file must be in `.claude/agents/` with valid frontmatter; restart.
- Never delegated automatically → description too vague; add "Use proactively when…".

**Next:** [Lab 07 — Plugins](07-plugins.md)
