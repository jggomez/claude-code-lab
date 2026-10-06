# 07 — Bonus: Orchestration and Loop Engineering

**Read time:** ~5 min · **Used in:** [lab 09 (bonus)](../lab/09-bonus-orchestration.md) · **Needs:** theory 04 (loops) and 05 (subagents)

So far you used one agent, or one specialist at a time. This bonus combines everything: **several agents working together, inside a loop that you designed**.

## 1. Orchestration

An **orchestrator** is the main session. It does not write the code itself; it splits the work, hands pieces to subagents, and combines the results.

```
                    ┌─► backend-dev  ──┐
 orchestrator ──────┤                  ├──► integrate ──► verifier ──► verdict
 (main session)     └─► frontend-dev ──┘     (fan-in)    (judge)
        fan-out (parallel)
```

Patterns:
| Pattern | Shape | Use when |
|---|---|---|
| **Fan-out / fan-in** | Parallel workers, then merge | Pieces are independent (backend vs. frontend) |
| **Pipeline** | A → B → C, each gated | Each stage needs the previous one's output |
| **Producer / judge** | One builds, another evaluates | You need an unbiased check |

### The contract is what makes parallel work possible
Two agents can work at the same time **only if they agree on the interface first**. Write the contract *before* launching them: endpoint, request, response, status codes, error shape. In TaskBoard that is the API table in `spec.md`, extended per feature.

### Keep workers from colliding
- Give each worker its own **files** (backend → `src/`, frontend → `public/`).
- Or isolate them in separate git worktrees (`isolation: worktree` in the agent frontmatter), then merge.
- Only the orchestrator touches shared files (spec, contract).

## 2. Loop engineering

Running a loop is easy. **Designing** one so it ends correctly, cheaply and honestly is the skill.

A well-engineered loop has:

| Part | Question it answers | Example |
|---|---|---|
| **Definition of done** | What exactly must be true? | tests green AND UI verified AND no console errors |
| **Objective checks** | How is each condition measured? | `npm test`, browser tools, `git diff --stat test/` |
| **Budget** | When do we give up? | max 3 rounds, max N turns |
| **Escalation rule** | When does a human step in? | Same failure twice → stop and ask |
| **Guardrails** | What must never change? | Tests are read-only during implementation |
| **State** | What survives between iterations? | Contract + report files, not chat memory |

### Reward hacking
A loop optimizes for "the check passes", not for "the software is right". Common cheats: deleting or weakening a test, hard-coding the expected value, catching and swallowing an error. Defenses:
1. **Write the tests first** (red) so they define the target.
2. **Make them read-only** while implementing (`deny` rules for `Edit(test/**)` / `Write(test/**)`).
3. **Separate builder and judge.**
4. **Review the diff**, especially anything under `test/`.

### The independent judge
The agent that wrote the code is a poor judge of it. Give the final verdict to a **different** agent with **read-only** tools (it can run tests and curl, but not edit). The builder cannot declare itself done; only the judge's `APPROVED` ends the loop.

```
round 1: build → judge: REJECTED (2 findings) → fix
round 2: build → judge: REJECTED (1 finding)  → fix
round 3: build → judge: APPROVED              → stop
(any round > 3, or the same finding twice → escalate to the human)
```

## 3. Measure it
After a run, record: rounds, agents used, rough cost (`/context`, usage), whether tests were touched, and what the judge caught that the builder missed. Loop design improves with numbers, not impressions.

## Check yourself
- Why must the contract exist before the parallel agents start?
- Name three cheats a loop can use to "pass", and a defense for each.
- Why can't the implementer be the final judge?
- What is the escalation rule for?

**Back to:** [README](../README.md)
