# Claude Code Lab for Beginners (90 min)

Learn Claude Code by building **TaskBoard**, a tiny web app (HTML + Tailwind + vanilla JS front, Node.js + Express back, no database). The app is a vehicle — the real subject is the **harness**: everything around the model that makes Claude Code useful.

## What you will learn
`Basics → Harness → Skills → MCP → Loops → Custom agents → Plugins`

## Repository layout
```
.
├── README.md
├── project-base/          # what you start with
│   ├── spec.md            # user stories + API contract
│   └── stack.md           # tech stack and conventions
├── theory/                # short readings (~30 min total, read before the session)
│   ├── 01-fundamentals.md
│   ├── 02-harness.md
│   ├── 03-skills-and-mcp.md
│   ├── 04-loops.md
│   ├── 05-custom-agents.md
│   ├── 06-plugins.md
│   └── glossary-and-cheatsheet.md
└── lab/                   # step-by-step hands-on
    ├── 00-setup.md
    ├── 01-bases.md
    ├── 02-harness-hooks-permissions.md
    ├── 03-skills.md
    ├── 04-mcp.md
    ├── 05-loops.md
    ├── 06-custom-agents.md
    ├── 07-plugins.md
    └── 08-wrap-up-and-challenges.md
```

## Prerequisites
- Node.js 20+
- Git
- Claude Code installed and an account to log in
- Chrome/Chromium (for the MCP lab)

## Agenda

| Min | Block | Lab | Theory |
|---|---|---|---|
| 0–8 | Setup + intro | [00](lab/00-setup.md) | [01](theory/01-fundamentals.md) |
| 8–25 | Basics: CLAUDE.md, plan mode, build HU1–HU2 | [01](lab/01-bases.md) | [01](theory/01-fundamentals.md) |
| 25–35 | The harness: permissions and hooks | [02](lab/02-harness-hooks-permissions.md) | [02](theory/02-harness.md) |
| 35–50 | Skills: your own + skills.sh (HU3–HU4) | [03](lab/03-skills.md) | [03](theory/03-skills-and-mcp.md) |
| 50–58 | MCP: give Claude a browser | [04](lab/04-mcp.md) | [03](theory/03-skills-and-mcp.md) |
| 58–70 | Loops (HU5); Part C is stretch | [05](lab/05-loops.md) | [04](theory/04-loops.md) |
| 70–82 | Custom agents | [06](lab/06-custom-agents.md) | [05](theory/05-custom-agents.md) |
| 82–90 | Plugins + wrap-up | [07](lab/07-plugins.md), [08](lab/08-wrap-up-and-challenges.md) | [06](theory/06-plugins.md) |

Short on time? HU6, the headless script (lab 05, part C), and the MCP stretch items are optional. The plugin block can be an instructor demo.

## How each lab is written
**Goal · Time · Theory link · Numbered steps with copy-paste prompts · Checkpoint · What to observe · If something fails.** Do not move on until the checkpoint boxes are ticked.

## Starting the lab
1. Read [theory/01-fundamentals.md](theory/01-fundamentals.md) and [theory/02-harness.md](theory/02-harness.md).
2. Open [lab/00-setup.md](lab/00-setup.md).

## A note on versions
Claude Code evolves quickly. Commands, flags and file formats here were checked against the official docs (https://code.claude.com/docs) but may change. If something doesn't match, trust `/help`, `claude --help` and the docs — and treat it as a good exercise in reading them.
