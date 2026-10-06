# Lab 09 (Bonus) — Orchestration, Loop Engineering and an Independent Judge

**Time:** 35–40 min · **Theory:** [07 — Orchestration and Loop Engineering](../theory/07-orchestration-and-loop-engineering.md)
**Needs:** Labs 02 (permissions), 05 (loops) and 06 (subagents). The Playwright MCP from Lab 04 is optional but recommended.

## Goal
Build one feature — **edit a task's title** — with a small team:

```
          ┌─► backend-dev  ──┐
 you ─► orchestrator          ├─► integrate ─► verifier (judge) ─► APPROVED?
 (main     └─► frontend-dev ─┘                      │ no
 session)    parallel, same contract                └─► fix and repeat (max 3 rounds)
```

You will practice: **contract-first design**, **parallel subagents**, a **loop with a budget and escalation rule**, **tests the builders can't touch**, and a **judge that can't edit code**.

## Part 0 — Prepare (3 min)
1. `npm test` is green and your work is committed (`git status` is clean).
2. Create a branch so you can compare or discard:
   ```bash
   git checkout -b bonus/edit-task
   ```
3. Make sure the story exists. If you didn't create one in Lab 03, run:
   ```
   /add-user-story Edit a task's title inline
   ```
   Note its number; below it is called **the edit story**.

## Part 1 — Contract first (5 min)
Parallel agents need an agreed interface. Use plan mode (Shift+Tab or `/plan`):
```
Read the edit story in docs/spec.md. Write docs/contracts/edit-task.md with the exact contract:
- PATCH /api/tasks/:id accepts { "title": string } (and still accepts { "done": boolean })
- success response, every error case with status codes and JSON error shape
- UI behavior: how the user starts editing, saves, cancels, and what error message shows for an empty title
- the file ownership: backend-dev only changes src/ and test/, frontend-dev only changes public/
Don't write any code.
```
Read it. Fix ambiguities now: they are 10× cheaper than after the agents run. Approve, then commit:
```
!git add -A && git commit -m "Contract for edit task"
```

## Part 2 — Create the team (5 min)

Create three subagents in `.claude/agents/`.

**`backend-dev.md`**
```markdown
---
name: backend-dev
description: Implements backend changes for TaskBoard (Express API in src/) following a contract in docs/contracts/. Use for API work.
tools: Read, Grep, Glob, Edit, Write, Bash
model: sonnet
---

You are a backend developer. Follow the contract file you are given EXACTLY.
- Only modify files under src/. Never touch public/ or test/.
- Run `npm test` after changes and fix failures in the code, never in the tests.
- Keep the stack from docs/stack.md (Express only, no new dependencies).
Report: files changed, how you satisfied each contract point, and anything ambiguous.
```

**`frontend-dev.md`**
```markdown
---
name: frontend-dev
description: Implements frontend changes for TaskBoard (HTML, Tailwind CDN, vanilla JS in public/) following a contract in docs/contracts/. Use for UI work.
tools: Read, Grep, Glob, Edit, Write
model: sonnet
---

You are a frontend developer. Follow the contract file you are given EXACTLY.
- Only modify files under public/. Never touch src/ or test/.
- Tailwind via CDN, vanilla JS, fetch. No new libraries.
- Handle the error cases and UI behavior described in the contract.
Report: files changed, how you satisfied each UI point in the contract, and anything ambiguous.
```

**`verifier.md`** — the judge, **read-only on code**
```markdown
---
name: verifier
description: Independent judge. Verifies a TaskBoard feature against its contract and acceptance criteria and issues APPROVED or REJECTED. Use after implementation, never to implement.
tools: Read, Grep, Glob, Bash
model: sonnet
---

You are an independent QA judge. You did not write this code and you must not change it.
1. Read the contract in docs/contracts/ and the story in docs/spec.md.
2. Run `npm test`. Run `git diff --stat` and check that nothing under test/ was modified since the "Contract for edit task" commit's tests were added (report any change as a finding).
3. Exercise the API with curl against http://localhost:3000 for every success and error case in the contract.
4. If browser tools are available, verify the UI behavior in the contract.
5. Output a table: Criterion | PASS/FAIL | Evidence.
6. End with exactly one line: `VERDICT: APPROVED` (only if every row is PASS) or `VERDICT: REJECTED` plus a numbered list of findings.
Never edit files.
```
Check with `/agents`; restart `claude` if they don't show.

## Part 3 — Red first: tests define the target (5 min)
```
Write the tests for the edit-task contract in test/ (success, empty title → 400, unknown id → 404, still-works-for-done). Do NOT implement the feature. Run npm test and show me the new tests fail.
```
**Checkpoint:** red tests. Commit them: `!git add -A && git commit -m "Failing tests for edit task"`.

### Lock the tests (guardrail)
The builders must not "fix" the tests. Create or edit `.claude/settings.local.json` (yours, not committed):
```json
{
  "permissions": {
    "deny": ["Edit(test/**)", "Write(test/**)"]
  }
}
```
Check with `/permissions`. (Remember to remove this after the exercise.)

## Part 4 — Orchestrate with a designed loop (10–12 min)

Make sure the app runs in another terminal (`npm start`, or `npm run dev` to restart on changes) and set **accept edits** mode (Shift+Tab) so the loop isn't interrupted. Then paste:

```
You are the orchestrator for the edit-task feature. Do not write code yourself.

Inputs: docs/contracts/edit-task.md and the failing tests in test/.

Definition of done (ALL must be true):
1. `npm test` passes with 0 failures.
2. The verifier subagent returns `VERDICT: APPROVED`.
3. `git diff --stat test/` shows no changes since the "Failing tests for edit task" commit.

Process, in rounds:
- Round start: launch backend-dev and frontend-dev IN PARALLEL, each with the contract. Wait for both reports.
- Then launch the verifier subagent. Only its verdict counts; you and the developers may not declare the work done.
- If REJECTED: turn the findings into targeted fix tasks, send each to the right developer, start the next round.

Budget and escalation:
- Maximum 3 rounds.
- If the same finding appears in two consecutive rounds, STOP and ask me.
- If round 3 is still REJECTED, STOP and summarize what blocks you.

When done (or stopped), report: rounds used, which agent did what, final verdict, and the evidence.
```

Watch for:
- Both developers launched **at the same time** (two agents running together).
- The verifier reporting a table with evidence.
- If it's rejected, the orchestrator creating *targeted* fixes, not rewriting everything.

> Prefer a self-paced loop? Prefix the prompt with `/loop` (no interval), as in Lab 05 Part A, and add "stop the loop when done". Either way, the stop rule is the definition of done.

## Part 5 — Audit the result (4 min)
```bash
git diff --stat           # who changed what: src/ and public/ only?
git diff --stat HEAD -- test/   # must be empty
```
Then try it by hand: edit a task in the browser, try an empty title, cancel.

Fill this table in your notes:

| Metric | Your result |
|---|---|
| Rounds used | |
| Findings the verifier caught that the developers missed | |
| Tests modified? (must be no) | |
| Did any agent leave its own files? | |
| Rough cost (`/context` or usage) | |

## Part 6 — Clean up and commit (2 min)
Remove the temporary guardrail (delete the `deny` rules from `.claude/settings.local.json`), then:
```
!git add -A && git commit -m "Edit task via orchestrated team"
```

## Checkpoint
- [ ] A contract file existed **before** any implementation
- [ ] backend-dev and frontend-dev ran in parallel and stayed in their own folders
- [ ] The tests were red first and unchanged at the end
- [ ] The verifier (read-only) issued the verdict, not the builders
- [ ] The loop stopped by its own rule: APPROVED, or escalation after the budget
- [ ] You can name two ways the loop could have cheated, and the guardrail that blocked each

## What to observe
- Parallel work is only safe because of the **contract** and **file ownership**.
- A loop is as honest as its checks. The locked tests and the independent judge are what make "done" mean something.
- Budget + escalation rule turn a runaway loop into a predictable process.

## Stretch
1. **Bad-loop experiment:** reset (`git checkout . && git clean -fd src public`, keep the tests) and run the same task with a sloppy prompt — no definition of done, no budget, tests unlocked, one agent builds *and* declares success. Compare rounds, cost and quality with the table above. Did it touch the tests?
2. **Worktree isolation:** add `isolation: worktree` to the two developer agents' frontmatter and rerun. How do the changes get merged?
3. **State in files:** make the orchestrator write `docs/contracts/edit-task.report.md` after every round so a fresh session could resume the work.
4. **Third gate:** add a `code-reviewer` (Lab 06) as a gate before the verifier.

## If something fails
- Only one agent runs, not both → say explicitly "launch both in the same step, in parallel".
- Developers edit outside their folder → tighten their prompt; add `deny` rules for the other folder in `.claude/settings.local.json`.
- Developers complain they can't write tests → expected: the tests were written in Part 3. If the tests are wrong, stop, unlock, fix the tests yourself, and relock.
- Verifier approves without evidence → require the evidence table in its prompt; reject verdicts without it.
- Agents aren't found → files must be in `.claude/agents/`; restart `claude`.
- Frontmatter fields differ in your version → check `/agents` and the docs.

**Back to:** [README](../README.md)
