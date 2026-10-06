# Lab 04 — MCP: Give Claude a Browser

**Time:** 10 min · **Theory:** [03 — Skills and MCP](../theory/03-skills-and-mcp.md)

## Goal
Connect the Playwright MCP server so Claude can open the running app, interact with it and report what it sees.

## Steps

### 1. Add the server (project scope)
In a normal terminal (not inside Claude), from the `taskboard/` folder:
```bash
claude mcp add --scope project playwright -- npx @playwright/mcp@latest
```
- `--scope project` writes `.mcp.json` so teammates get it too.
- Everything after `--` is the command that starts the server.

Restart `claude`. Approve the project MCP server when prompted.

### 2. Verify
```
/mcp
```
`playwright` should show as connected, with its tools listed. (If the first run downloads a browser, give it a minute.)

### 3. Allow its tools (optional)
In `/permissions` or `.claude/settings.json`, you can pre-approve the server's tools with a rule like `mcp__playwright`. Otherwise Claude asks each time — fine for a lab.

### 4. Test the real UI
Make sure the app is running (`npm start` in another terminal), then:
```
Using the browser tools, open http://localhost:3000, add a task called "Buy milk",
mark it done, then delete it. After each step tell me what you see and take a screenshot.
```
Watch the browser window: Claude is clicking your app.

### 5. Find a bug with it
```
Try to add a task with only spaces as the title. Does the UI show an error as the spec says (HU2)? Report any mismatch with docs/spec.md.
```

### 6. Compare context cost
```
/context
```
Note how much the MCP tool definitions add. This is why you only install what you need.

### 7. Commit
```
!git add -A && git commit -m "Playwright MCP"
```

## Checkpoint
- [ ] `/mcp` shows `playwright` connected
- [ ] Claude drove the browser through add → complete → delete
- [ ] `.mcp.json` exists in the project root
- [ ] You can explain why MCP servers deserve the same trust as running code

## What to observe
Claude went from "I think the UI works" to **"I saw it work"**. MCP extends what the model can verify, which feeds directly into loops (next lab).

## Safety note
An MCP server runs as you. Only install servers from sources you trust, and review `.mcp.json` in repos you clone.

## If something fails
- `npx` hangs → check Node/network; run `npx @playwright/mcp@latest --help` manually first.
- Not listed in `/mcp` → restart Claude and approve the project server.
- Flags differ in your version → `claude mcp add --help`.
- No Playwright available? Substitute the Claude-in-Chrome extension or any browser MCP you have.

**Next:** [Lab 05 — Loops](05-loops.md)
