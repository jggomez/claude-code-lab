# Lab 03 — Skills

**Time:** 15 min · **Theory:** [03 — Skills and MCP](../theory/03-skills-and-mcp.md)

## Goal
Create your own skill that adds well-formed user stories to `docs/spec.md`, **install a community skill from [skills.sh](https://skills.sh)** to improve the app's UI, and use both to implement HU3 and HU4.

## Steps

### 1. Create the skill
```bash
mkdir -p .claude/skills/add-user-story
```
Create `.claude/skills/add-user-story/SKILL.md`:
````markdown
---
name: add-user-story
description: Adds a new user story to docs/spec.md using the project template. Use when the user asks to add, write, define or document a user story or requirement.
---

# Add a user story

Input: a short description of the feature (`$ARGUMENTS`, or ask if empty).

1. Read `docs/spec.md` and find the highest existing `HU<n>`; the new story is `HU<n+1>`.
2. Append a section before "API contract", using exactly this template:

```markdown
### HU<n> — <Short title>
**As a** user, **I want** <goal> **so that** <benefit>.

Acceptance criteria:
- <testable criterion>
- <testable criterion>
- <error case with the HTTP status if it touches the API>
```

3. Criteria must be testable (observable behavior, status codes, UI text). 2–4 criteria.
4. If the story adds or changes an endpoint, update the "API contract" table too.
5. Do not implement anything. Report the new HU number and title.
````

### 2. Check it loaded
Restart `claude` (skills are discovered at startup in some setups; if you edited an existing skill it hot-reloads). Type `/` and look for `add-user-story` in the list.

### 3. Manual invocation
```
/add-user-story Export all tasks as a downloadable JSON file
```
Open `docs/spec.md`: a new `HU7` should be added in the exact template.

### 4. Automatic invocation
```
Write a user story for editing a task's title inline.
```
Without typing the slash command, Claude should load the skill (you'll see it announced). If it doesn't, your `description` isn't specific enough — sharpen it.

### 5. Find a community skill (skills.sh)
[skills.sh](https://skills.sh) is a public directory of ready-made skills, installed with the `skills` CLI. Search from the terminal (not inside Claude), or browse the site's *Design & UI* and *Testing* topics:
```bash
npx skills find frontend design
```

### 6. Inspect before you install
A skill is instructions (and sometimes scripts) that Claude will follow **with your permissions**. Treat it like code you are about to run:
- Open its page on skills.sh and read the `SKILL.md`.
- Prefer verified/official publishers and popular skills.
- Check for scripts or commands you don't understand.

### 7. Install it into the project
Example (pick one from your search; names on skills.sh change over time):
```bash
npx skills add vercel-labs/agent-skills --skill web-design-guidelines -a claude-code -y
```
- `--skill <name>` installs a single skill from the repo.
- `-a claude-code` targets Claude Code; `-y` skips confirmations.
- Without flags the CLI asks interactively, and you can choose project vs. global install.

Restart `claude` and type `/` — the new skill should be listed next to `add-user-story`. Look at the folder the CLI created under `.claude/skills/` and read the files.

> Other options to try: a `frontend-design` skill for better-looking UIs, or a `tdd` / `code-review` skill for the backend. Check `npx skills --help` if flags differ in your version.

### 8. Use both skills to build and improve
Build the stories with your spec workflow:
```
Implement HU3 and HU4 from docs/spec.md. Backend with tests first, then the frontend.
```
Then improve the UI with the community skill:
```
Using the web-design-guidelines skill, review public/index.html and public/app.js and fix the issues it finds
(accessibility, focus states, labels, responsive layout). Keep the tests green.
```
Your hook from Lab 02 runs the tests as Claude works. Compare the result in the browser before/after (`!git diff --stat`).

### 9. Commit
```
!git add -A && git commit -m "Skill add-user-story, HU3, HU4"
```

## Checkpoint
- [ ] `add-user-story` appears when you type `/`
- [ ] The new story follows the template exactly
- [ ] You triggered your skill **both** manually and automatically
- [ ] You installed a skills.sh skill, read its `SKILL.md` first, and Claude used it on the UI
- [ ] Completing and deleting tasks works in the browser; `npm test` green

## What to observe
- Only the skill's *description* was in context until it was needed (progressive disclosure).
- A skill gives the *same* result every time — a template you control.
- Community skills let you borrow expertise you didn't write — but they are third-party instructions: read before installing.

## Stretch
Add `disable-model-invocation: true` to the frontmatter. What changes? (Now only `/add-user-story` triggers it.)

## If something fails
- Skill not listed → path must be `.claude/skills/<name>/SKILL.md`; `name` must match; restart Claude.
- Never auto-triggers → rewrite the description: *what it does + when to use it*.
- `npx skills` fails or the skill name doesn't exist → the directory changes often; run `npx skills find` and use a current name.
- Installed to the wrong place → rerun with `-a claude-code`, or check `.claude/skills/` (project) vs `~/.claude/skills/` (global).

**Next:** [Lab 04 — MCP](04-mcp.md)
