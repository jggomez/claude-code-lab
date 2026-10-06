# Lab 03 — Skills

**Time:** 12 min · **Theory:** [03 — Skills and MCP](../theory/03-skills-and-mcp.md)

## Goal
Create a skill that adds well-formed user stories to `docs/spec.md`, then use it to add and implement HU3 and HU4.

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

### 5. Use the spec to build
Undo nothing; the extra stories are fine as backlog. Now implement the existing ones:
```
Implement HU3 and HU4 from docs/spec.md. Backend with tests first, then the frontend.
```
Your hook from Lab 02 runs the tests as Claude works.

### 6. Commit
```
!git add -A && git commit -m "Skill add-user-story, HU3, HU4"
```

## Checkpoint
- [ ] `add-user-story` appears when you type `/`
- [ ] The new story follows the template exactly
- [ ] You triggered the skill **both** manually and automatically
- [ ] Completing and deleting tasks works in the browser; `npm test` green

## What to observe
- Only the skill's *description* was in context until it was needed (progressive disclosure).
- A skill gives the *same* result every time — a template you control.

## Stretch
Add `disable-model-invocation: true` to the frontmatter. What changes? (Now only `/add-user-story` triggers it.)

## If something fails
- Skill not listed → path must be `.claude/skills/<name>/SKILL.md`; `name` must match; restart Claude.
- Never auto-triggers → rewrite the description: *what it does + when to use it*.

**Next:** [Lab 04 — MCP](04-mcp.md)
