# Lab 05 — Loops

**Time:** 12 min · **Theory:** [04 — Loops](../theory/04-loops.md)

## Goal
Use three kinds of loops: a verification loop, a recurring `/loop`, and a headless script loop with a hard cap.

## Part A — Verification loop (4 min)

### 1. Write the goal with a check and a stop condition
```
Implement HU5 (filter All / Pending / Done) from docs/spec.md.
Add tests for GET /api/tasks?status=pending|done first.
Run `npm test` after every change and keep iterating until all tests pass.
If you are still failing after 5 attempts, stop and tell me what's blocking you.
```
Watch the loop: *edit → test → read failure → edit …* until green.

### 2. Close the loop with the browser
```
Now use the browser tools to verify the three filter buttons on http://localhost:3000, including that the filter persists after adding a task. Fix anything that fails.
```

## Part B — Recurring loop with `/loop` (5 min)

**Idea:** `/loop` repeats a prompt automatically on a timer while your Claude session stays open. Here it will run your tests every minute, like a watchdog, while *you* edit code. You need **two windows**:

| Window | What it is | Used for |
|---|---|---|
| **A** | Terminal with `claude` running in `taskboard/` | Runs the loop |
| **B** | Your code editor (VS Code, etc.) with `taskboard/` open | Where you break and fix code |

> Tests must be green before you start. In window A run `!npm test` — if it fails, fix it first.

### 1. Start the loop (window A)
Type this in the Claude prompt and press Enter:
```
/loop 1m run npm test and tell me only if something fails
```
- `1m` = every 1 minute (use `2m` or `5m` in real projects).
- The text after the interval is the prompt that is repeated.
- Claude runs it once right away. Expected: tests pass and it says nothing is wrong (or a short "all green").

> Leave the interval out and Claude picks its own pace: `/loop run npm test and tell me only if something fails`.

### 2. Break something on purpose (window B)
Open `src/app.js` and change a status code, for example the `201` returned by `POST /api/tasks` to `200`. **Save the file** (Ctrl+S / Cmd+S).

> Don't see a test that would catch it? Pick a change your tests do cover, e.g. make the `400` for an empty title a `500`.

### 3. Wait for the next tick (window A)
Don't type anything. Within about a minute Claude runs the test again by itself and reports the failure (which test failed and why). That message is the loop doing its job.

### 4. Fix it
Either revert the line yourself in the editor, or tell Claude in window A:
```
Fix the failing test by restoring the original behavior in src/app.js.
```
Wait for the next tick: Claude should now stay quiet (or say all green).

### 5. Stop the loop (window A)
A loop keeps running (and spending tokens) until you stop it. Say:
```
Stop the loop.
```
If it keeps firing, press `Esc` and repeat, or run `/clear`, or exit Claude with `/exit`. Confirm no new test runs appear after a minute.

### Checkpoint for Part B
- [ ] You started the loop and saw the first run
- [ ] After breaking the code and saving, Claude reported the failure **without you asking**
- [ ] After the fix, the reports stopped
- [ ] You stopped the loop

> `/loop` only lives as long as your session is open. For jobs that must run when your laptop is closed, you'd use a scheduled cloud routine (`/schedule`) instead — not part of this lab.

## Part C — Headless loop (3 min, stretch if short on time)

Make sure the Part B loop is stopped first.

### 1. A script with a hard cap
Create `fix-until-green.sh`:
```bash
#!/usr/bin/env bash
MAX=5
for i in $(seq 1 $MAX); do
  echo "== attempt $i/$MAX =="
  if npm test --silent; then
    echo "Green on attempt $i"; exit 0
  fi
  claude -p "npm test is failing. Fix the application code, NOT the tests. Keep changes minimal." \
    --max-turns 8 \
    --allowedTools "Read,Edit,Bash(npm test)"
done
echo "Still failing after $MAX attempts"; exit 1
```
```bash
chmod +x fix-until-green.sh
```

### 2. Break and heal
Introduce a bug in `src/tasks.js` yourself, then run `./fix-until-green.sh`.

### 3. Review what it did
```
!git diff
```
Did it fix the code or weaken a test? **Always review.**

### 4. Commit
```
!git add -A && git commit -m "HU5 + loops"
```

## Checkpoint
- [ ] HU5 works in the browser and `npm test` is green
- [ ] You saw Claude iterate on a failing test by itself
- [ ] `/loop` reported a failure you introduced
- [ ] The script stopped by itself (success or after 5 attempts)

## What to observe
- Loops converge only with an **objective check** and a **stop condition**.
- Each headless iteration starts with a fresh context.
- Cost scales with iterations: caps are not optional.

## If something fails
- `/loop` not found → update Claude Code; as a fallback, use the shell loop in Part C.
- Loop never reports → did you save the file? Is `npm test` actually failing? Wait a full interval; use `1m` for the lab.
- Loop reports every minute even when green → rewrite the prompt: `...and say nothing if all tests pass`.
- Headless run asks for permissions → add the needed tool to `--allowedTools`.
- Script weakens tests → tighten the prompt, or add a `deny` rule for `Edit(test/**)`.

**Next:** [Lab 06 — Custom Agents](06-custom-agents.md)
