# Lab 05 — Loops

**Time:** 17 min (Part A 8 + Part B 5) + 6 min stretch (Part C) · **Theory:** [04 — Loops](../theory/04-loops.md)

## Goal
Use three kinds of loops: a goal loop that runs until an objective is met, a recurring `/loop` watchdog, and a headless script loop with a hard cap.

## Three kinds of loop — don't mix them up

| | What it does | You give it | It stops when |
|---|---|---|---|
| **A. Goal loop** (with `/loop`, no interval) | Keeps working **until an objective is met** (this is the "loop until it's done without errors" one) | A definition of done + a check | The check passes (or the attempt limit is hit) |
| **B. `/loop 1m ...`** (fixed interval) | Re-runs a prompt **on a timer** (a watchdog) | An interval + a prompt | You stop it |
| **C. Headless script** | A script that calls `claude -p` repeatedly | A script with a cap | Success or max attempts |

Part A is the main exercise. B and C are variations.

## Part A — Goal loop: "don't stop until everything passes" (8 min)

**Idea:** you don't tell Claude *how*, you tell it **what "done" means** and how to check it. Claude then loops by itself — implement → check → read errors → fix → check again — and only stops when every check passes. The loop is only as good as its **objective check** and its **stop condition**.

### 1. Prepare: let it run without asking every step
In the Claude prompt press **Shift+Tab** until the mode shows *accept edits*. Otherwise Claude pauses for approval on each edit and the loop keeps stopping. (The permission rules from Lab 02 already allow `npm test`.)

Make sure the app is running in another terminal (`npm start`) and the Playwright MCP from Lab 04 is connected (`/mcp`).

### 2. Red first: write the tests that define "done"
```
Read HU5 and HU6 in docs/spec.md. Do NOT implement anything yet.
Write tests in test/ for every acceptance criterion of HU5 (filtering) and HU6 (priority + pending counter).
Then run npm test and show me that they fail.
```
Checkpoint: you see **red** — new tests failing because the feature doesn't exist. That failing suite is your objective.

### 3. Give the objective and run it with `/loop`
Use `/loop` **without an interval**: Claude paces itself, keeps coming back to the task, and ends the loop on its own when the goal is met. Paste this and **don't interrupt**:
```
/loop Goal: HU5 and HU6 fully working. You are done ONLY when ALL of these are true:
1. `npm test` passes with 0 failures (do not edit or delete the tests I just wrote).
2. Using the browser tools on http://localhost:3000: the All / Pending / Done buttons filter correctly, priority badges show, and the "N pending" counter updates after adding, completing and deleting a task.
3. The browser console has no errors.

Each iteration: implement or fix → run npm test → if anything fails, read the error and fix it → then verify in the browser. Keep iterating until 1, 2 and 3 are all true.
When all three are true, STOP the loop and report each condition with its evidence.
If you are still failing after 8 iterations, stop the loop and tell me exactly what is blocking you.
```
Watch it work. Count the iterations: each pass of *edit → `npm test` → read failure → edit again* is one. Your Lab 02 hook also runs the tests after every edit.

> **Why `/loop` here?** The loop itself is the mechanism that keeps Claude working until the objective is true, and it ends the moment the objective is met. A fixed interval (`/loop 2m ...`) is for watching, see Part B.
>
> **No `/loop` in your version?** Send the same text without `/loop`. A single prompt also iterates (implement → test → fix) until the conditions hold.

### 4. Verify it wasn't cheating
```
!git diff --stat test/
```
The tests you wrote in step 2 must be unchanged. Open the app yourself and try the filters.

### 5. Reflect
- What made Claude stop? *(All three conditions true — an objective check, not a feeling.)*
- What would have happened with "make the filters work" and no definition of done? *(It would stop at the first plausible-looking result.)*
- Why did we write tests first? *(They turn a vague goal into something the loop can verify.)*

### Checkpoint for Part A
- [ ] You saw red tests before any implementation
- [ ] Claude iterated several times on its own (via `/loop`) until `npm test` was green, then ended the loop itself
- [ ] It verified the UI in the browser and reported evidence for each condition
- [ ] The tests from step 2 were not modified
- [ ] You can name the 3 ingredients: **verifiable goal, stop condition, fast check**

> **Stretch:** add a 4th condition (e.g. "no failing request in the browser network log") and run again.

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

## Part C — Headless loop (6 min, stretch if short on time)

**Idea:** `claude -p "prompt"` runs Claude **without opening the chat**: it does the task, prints the result and exits. That lets a normal script call Claude, check if the tests pass, and call it again — a loop *you* control, with a hard cap on attempts.

Do this in a **regular terminal** (not inside the `claude` chat), in the `taskboard/` folder. Make sure the Part B loop is stopped.

```
 script ──► npm test ──► green? ──yes──► done
              │ no
              ▼
        claude -p "fix it"   (fresh context each time)
              │
              └──► repeat, max 5 attempts
```

### 1. Create the script file
The file lives in the **root of the project**, next to `package.json`:
```
taskboard/
├── package.json
├── fix-until-green.sh     ← new file (or fix-until-green.js on Windows)
├── src/
└── test/
```
Create it with your editor (**File → New File → save as `fix-until-green.sh`** in `taskboard/`), or from the terminal. Paste this content:

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

What each part does:
| Line | Meaning |
|---|---|
| `MAX=5` | Hard cap: never more than 5 attempts |
| `for i in $(seq 1 $MAX)` | Repeat up to 5 times |
| `if npm test --silent; then ... exit 0` | If tests pass, stop with success |
| `claude -p "..."` | Otherwise ask Claude (headless) to fix the code |
| `--max-turns 8` | Claude may take at most 8 steps per attempt |
| `--allowedTools "Read,Edit,Bash(npm test)"` | Claude may only read, edit and run `npm test` — nothing else, so no permission prompts |
| last two lines | Reached after 5 failed attempts: give up |

### 2. Make it executable (macOS / Linux / WSL / Git Bash)
```bash
chmod +x fix-until-green.sh
```
On Windows you can skip this and run `bash fix-until-green.sh`.

> **Windows without bash?** Use the Node.js version instead. Create `fix-until-green.js` in the same place:
> ```js
> const { spawnSync } = require('node:child_process');
> const MAX = 5;
> for (let i = 1; i <= MAX; i++) {
>   console.log(`== attempt ${i}/${MAX} ==`);
>   if (spawnSync('npm', ['test', '--silent'], { stdio: 'inherit', shell: true }).status === 0) {
>     console.log(`Green on attempt ${i}`);
>     process.exit(0);
>   }
>   spawnSync('claude', ['-p', 'npm test is failing. Fix the application code, NOT the tests. Keep changes minimal.',
>     '--max-turns', '8', '--allowedTools', 'Read,Edit,Bash(npm test)'], { stdio: 'inherit', shell: true });
> }
> console.log(`Still failing after ${MAX} attempts`);
> process.exit(1);
> ```
> Run it with `node fix-until-green.js`.

### 3. Break the code on purpose
Open `src/tasks.js` and introduce a bug that your tests catch — for example, make the new task's `done` start as `true` instead of `false`. Save it. Check it fails:
```bash
npm test
```
(You should see a failing test. If not, pick a different bug.)

### 4. Run the script
```bash
./fix-until-green.sh          # macOS / Linux / WSL / Git Bash
bash fix-until-green.sh       # Windows with Git Bash
node fix-until-green.js       # Node version, any OS
```
Expected output: `== attempt 1/5 ==` with a failing test → Claude works for a while → `== attempt 2/5 ==` → `Green on attempt 2`. The script stops by itself.

### 5. Review what it did
```bash
git diff
```
Did Claude fix the code, or weaken a test? **Always review** — a loop that "passes" can still be wrong.

### 6. Commit (and don't commit the helper script unless you want to)
```bash
git add -A && git commit -m "HU5 + loops"
```

## Checkpoint
- [ ] HU5 works in the browser and `npm test` is green
- [ ] You saw Claude iterate on a failing test by itself
- [ ] `/loop` reported a failure you introduced
- [ ] The script file exists in the project root and stopped by itself (success or after 5 attempts)

## What to observe
- Loops converge only with an **objective check** and a **stop condition**.
- Each headless iteration starts with a fresh context.
- Cost scales with iterations: caps are not optional.

## If something fails
- `/loop` not found → update Claude Code; as a fallback, use the shell loop in Part C.
- Loop never reports → did you save the file? Is `npm test` actually failing? Wait a full interval; use `1m` for the lab.
- Loop reports every minute even when green → rewrite the prompt: `...and say nothing if all tests pass`.
- `claude: command not found` inside the script → run it from a terminal where `claude` works.
- `./fix-until-green.sh: Permission denied` → run `chmod +x fix-until-green.sh`, or use `bash fix-until-green.sh`.
- Script says `Green on attempt 1` immediately → your bug isn't caught by the tests; pick another one.
- Headless run asks for permissions → add the needed tool to `--allowedTools`.
- Script weakens tests → tighten the prompt, or add a `deny` rule for `Edit(test/**)`.

**Next:** [Lab 06 — Custom Agents](06-custom-agents.md)
