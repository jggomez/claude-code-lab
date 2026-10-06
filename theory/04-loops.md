# 04 — Loops

**Read time:** ~4 min · **Used in:** [lab 05](../lab/05-loops.md)

A **loop** is when Claude repeats work until a condition is met, or repeats a task on a schedule. There are three kinds.

## Which loop?

| Loop | Trigger | Stops when | Use it to |
|---|---|---|---|
| **Goal loop** (`/loop` with no interval, or a plain prompt) | One prompt with a definition of done | The check passes | Get a task finished without errors |
| **`/loop`** | A timer | You stop it | Watch something (tests, a URL, a deploy) |
| **Headless `claude -p`** | A script | Success or max attempts | Automate or run in CI |

## 1. The goal loop (inside one prompt)
The agentic loop already iterates. You make it **converge** by giving it a *definition of done* and an objective check (ideally tests written first, so they start red):

```
Implement HU5. Run `npm test` after every change and keep going until all tests pass.
Stop and ask me if you are stuck after 5 attempts.
```

Ingredients of a good loop:
- **A verifiable goal** — tests green, a URL returns 200, a linter is clean.
- **A stop condition** — success *and* a limit ("max 5 attempts").
- **A fast check** — seconds, not minutes.

## 2. Recurring loops: `/loop`
Run a prompt on an interval while the session is open:

```
/loop 2m run npm test and tell me only if something fails
/loop 5m check that http://localhost:3000/api/tasks still returns 200
```
If you omit the interval, Claude paces itself. Good for watching tests, builds, CI or deploys while you work. For jobs that must run when your laptop is closed, use scheduled (cloud) routines (`/schedule`) instead.

## 3. Headless loops: `claude -p`
`-p` ("print") runs Claude **non-interactively**: prompt in, answer out. That makes it scriptable.

```bash
claude -p "Run npm test and fix any failing test" \
  --allowedTools "Read,Edit,Bash(npm test)" \
  --max-turns 10
```

Useful flags: `--allowedTools`, `--max-turns`, `--output-format text|json|stream-json`, `--continue`, `--permission-mode`.

Wrap it in a shell loop with a hard cap:

```bash
for i in 1 2 3 4 5; do
  npm test --silent && echo "green on attempt $i" && break
  claude -p "npm test is failing. Fix the code (not the tests)." --max-turns 8 \
    --allowedTools "Read,Edit,Bash(npm test)"
done
```
Each iteration starts with a **fresh context** — a popular pattern for long tasks because context never rots.

## Guardrails
| Risk | Mitigation |
|---|---|
| Infinite / runaway loops | Always cap iterations (`--max-turns`, loop counter) |
| Cost | Cap turns, use a cheaper model for simple checks (`--model haiku`) |
| Claude "fixes" tests by weakening them | Say "fix the code, not the tests"; review diffs; use a hook/review agent |
| Unsafe autonomy | Allow only the tools needed; run in a branch/worktree; never `--dangerously-skip-permissions` outside a sandbox |
| Context rot in long sessions | `/compact`, `/clear`, or fresh-context headless iterations |

## When NOT to loop
- No automatic way to check success → a loop just burns tokens.
- Ambiguous requirements → fix the spec first (plan mode).

## Check yourself
- What three ingredients make a loop converge?
- `/loop` vs `claude -p` — when each?
- Why start each headless iteration with a fresh context?

**Next:** [05 — Custom Agents](05-custom-agents.md)
