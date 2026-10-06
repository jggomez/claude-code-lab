# Lab 00 — Setup

**Time:** 8 min · **Theory:** [01 — Fundamentals](../theory/01-fundamentals.md)

## Goal
Have Claude Code running in an empty project folder with the spec and stack files in place.

## Prerequisites
- Node.js 20+ (`node -v`)
- A terminal and an editor
- A Claude account (Pro/Max/Team/Console) to log in

## Steps

### 1. Install Claude Code
Follow the official install instructions for your OS (https://code.claude.com/docs). Verify:
```bash
claude --version
```

### 2. Create the project
```bash
mkdir taskboard && cd taskboard
git init
mkdir docs
cp /path/to/lab-claude/project-base/spec.md  docs/spec.md
cp /path/to/lab-claude/project-base/stack.md docs/stack.md
```
> `git init` gives you a safety net: you can always see (`git diff`) and undo what Claude did.

### 3. Start Claude Code and log in
```bash
claude
```
Follow the login prompt the first time. Accept the trust prompt for the folder.

### 4. Look around
Type these, one at a time:
```
/help
/context
/model
```

## Checkpoint
- [ ] `claude` opens in `taskboard/`
- [ ] `docs/spec.md` and `docs/stack.md` exist
- [ ] `/context` prints a usage breakdown

## What to observe
Even an empty project already has context cost (system prompt + tools). Remember that number; we'll compare it later.

## If something fails
- `claude: command not found` → reopen the terminal / check PATH.
- Login loop → run `/login` inside Claude.

**Next:** [Lab 01 — Bases](01-bases.md)
