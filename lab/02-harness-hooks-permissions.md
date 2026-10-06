# Lab 02 — The Harness: Permissions and Hooks

**Time:** 10 min · **Theory:** [02 — The Harness](../theory/02-harness.md)

## Goal
Take control of the harness: stop approving the same safe commands, block dangerous ones, and make tests run automatically after every edit.

## Steps

### 1. Map the harness
Run these and note what each shows:
```
/memory
/permissions
/hooks
```
Draw (or look at) the diagram in theory 02: you are now looking at three of its boxes.

### 2. Permissions: allow the safe, deny the dangerous
Create `.claude/settings.json`:
```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run *)",
      "Bash(npm install)",
      "Bash(git status)",
      "Bash(git diff *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Read(./.env)"
    ]
  }
}
```
Check with `/permissions` that the rules appear. Then test the deny rule:
```
Delete the node_modules folder using rm -rf.
```
Claude should be unable to run it. (It may propose another way — that's your cue that `deny` rules are only as broad as you write them.)

### 3. Hook: tests after every edit
Create the script `.claude/hooks/run-tests.sh`:
```bash
#!/usr/bin/env bash
# Runs after Edit/Write. If tests fail, exit 2 so Claude sees the output.
[ -f package.json ] || exit 0
[ -d test ] || exit 0

output=$(npm test --silent 2>&1)
status=$?
if [ $status -ne 0 ]; then
  echo "Tests failed after your last edit:" >&2
  echo "$output" | tail -30 >&2
  exit 2
fi
exit 0
```
**macOS / Linux / WSL / Git Bash:**
```bash
chmod +x .claude/hooks/run-tests.sh
```

**Windows (PowerShell or CMD):** `chmod` doesn't exist and isn't needed — Windows has no executable bit, and the hook runs the script through `bash`, so skip this step. You only need `bash` available on your PATH (it comes with [Git for Windows](https://git-scm.com/download/win); check with `bash --version`).

> No bash on Windows? Use a Node.js version of the hook instead (Node is already a prerequisite). Create `.claude/hooks/run-tests.js`:
> ```js
> const { spawnSync } = require('node:child_process');
> const fs = require('node:fs');
>
> if (!fs.existsSync('package.json') || !fs.existsSync('test')) process.exit(0);
>
> const r = spawnSync('npm', ['test', '--silent'], { encoding: 'utf8', shell: true });
> if (r.status !== 0) {
>   console.error('Tests failed after your last edit:');
>   console.error(((r.stdout || '') + (r.stderr || '')).split('\n').slice(-30).join('\n'));
>   process.exit(2);
> }
> ```
> and register it with `"command": "node .claude/hooks/run-tests.js"` instead of the `bash` command below. It works on every OS.
Register it in `.claude/settings.json` (merge with the permissions block):
```json
{
  "permissions": { "...": "keep what you had" },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "bash .claude/hooks/run-tests.sh" }
        ]
      }
    ]
  }
}
```
Run `/hooks` to confirm it's listed (you may need to restart `claude` for settings changes to be picked up).

### 4. Break something on purpose
```
In src/tasks.js, make createTask return the title in uppercase. Don't touch the tests.
```
Watch: Claude edits → the hook runs the tests → failure output goes back to Claude → it reacts, without you asking.

### 5. Instruction vs. enforcement
Add this line to `CLAUDE.md`: `Always run npm test after editing code.`
Discuss: why is the hook still better? *(It always runs; the CLAUDE.md line is a request.)*

### 6. Commit
```
!git add -A && git commit -m "Harness: permissions + test hook"
```

## Checkpoint
- [ ] `/permissions` lists your allow/deny rules
- [ ] `/hooks` shows the `PostToolUse` hook
- [ ] After the intentional break, Claude noticed the failing test without being told
- [ ] You reverted the intentional break (`git checkout src/tasks.js` or ask Claude)

## What to observe
Hooks are **deterministic** — the harness runs them, not the model. This is the foundation for everything automated later.

## If something fails
- Hook never fires → check the JSON is valid, `matcher` is `Edit|Write`, script is executable (macOS/Linux), `bash` is on your PATH (Windows), restart Claude.
- Hook fires on a project with no tests → the guards (`[ -d test ] || exit 0`) skip it.

**Next:** [Lab 03 — Skills](03-skills.md)
