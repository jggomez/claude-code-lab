# Lab 01 — The Basics: CLAUDE.md, Plan Mode, Build

**Time:** 17 min · **Theory:** [01 — Fundamentals](../theory/01-fundamentals.md)

## Goal
Plan and build HU1 + HU2 (backend + frontend) from the spec, using plan mode and a good `CLAUDE.md`.

## Steps

### 1. Generate CLAUDE.md
Inside `claude`:
```
/init
```
Open `CLAUDE.md` in your editor. The folder only has docs, so it will be thin. Replace/extend it with:

```markdown
# TaskBoard

Tiny task manager used to learn Claude Code.

## Source of truth
- Requirements: @docs/spec.md
- Tech stack and conventions: @docs/stack.md

## Commands
- `npm start` — run on http://localhost:3000
- `npm test` — node --test

## Rules
- Follow the stack: Express + vanilla JS + Tailwind via CDN. No other dependencies.
- Implement one user story at a time and mention which HU you implemented.
- Every API change needs a test in `test/`.
```
> `@docs/spec.md` inside CLAUDE.md imports the file so Claude always has it.

Restart to reload it: `/clear` (or exit and run `claude` again).

### 2. Plan first (plan mode)
Press **Shift+Tab** until the status shows *plan mode* (or type `/plan`). Then:
```
Read docs/spec.md and docs/stack.md. Plan the implementation of HU1 and HU2:
project scaffolding, API, frontend and tests. List the files you will create.
Don't write any code yet.
```
Read the plan. Ask for a change, e.g.:
```
Also add a test for the 400 case on POST /api/tasks.
```
Approve the plan when it looks right (choose the option that proceeds with edits).

### 3. Build the backend
```
Implement the backend for HU1 and HU2 per the plan: package.json, src/app.js,
src/server.js, src/tasks.js and test/tasks.test.js. Then run npm install and npm test.
```
Approve the tool prompts as they appear. Notice what Claude does: *writes files → runs tests → reads output*.

### 4. Build the frontend
```
Now the frontend for HU1 and HU2 in public/. Use Tailwind via CDN and vanilla JS fetch.
Show "No tasks yet" when empty and an error message for invalid titles.
```

### 5. Run it
```
Start the server in the background and tell me the URL.
```
Open http://localhost:3000, add two tasks.

### 6. Practice context commands
```
/context
```
Compare with Lab 00. Then try:
```
/rewind
```
Look at the restore points, then cancel (Esc). Use `!git status` to run a shell command directly.

### 7. Commit
```
!git add -A && git commit -m "HU1, HU2"
```

## Checkpoint
- [ ] `curl localhost:3000/api/tasks` returns `[]` (or your tasks)
- [ ] You can add a task in the browser and it appears
- [ ] `npm test` is green
- [ ] You saw a plan **before** any file was written

## What to observe
- How much better the result is when the plan came first.
- The `@file` import in CLAUDE.md saved you from re-pasting the spec.

## If something fails
- Port 3000 busy → `lsof -i :3000` and stop the old process.
- Tests fail → paste the failure: `npm test fails with the output above, fix it.`

**Next:** [Lab 02 — Harness: Permissions and Hooks](02-harness-hooks-permissions.md)
