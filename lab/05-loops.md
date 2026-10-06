# Lab 05 — Loops

**Time:** 12 min · **Theory:** [04 — Loops](../theory/04-loops.md)

## Goal
Use three kinds of loops: a verification loop, a recurring `/loop`, and a headless script loop with a hard cap.

## Part A — Verification loop (6 min)

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

## Part B — Recurring loop with `/loop` (3 min)
Keep this running while you do Part C in another terminal:
```
/loop 2m run npm test and tell me only if something fails
```
(Without an interval, Claude chooses its own pace: `/loop run npm test and tell me only if something fails`.)

Introduce a failure on purpose from your editor (e.g. change a status code in `src/app.js`), save, and wait for the next tick. Revert it afterwards. Stop the loop when you're done (ask Claude to stop it, or press `Esc` and tell it to cancel the loop).

## Part C — Headless loop (4 min, stretch if short on time)

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
- `/loop` not found → update Claude Code; as a fallback, use the shell loop.
- Headless run asks for permissions → add the needed tool to `--allowedTools`.
- Script weakens tests → tighten the prompt, or add a `deny` rule for `Edit(test/**)`.

**Next:** [Lab 06 — Custom Agents](06-custom-agents.md)
