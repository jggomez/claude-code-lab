# 08 — Bonus: Spec-Driven Development (SDD)

**Read time:** ~4 min · **Used in:** [lab 10 (bonus)](../lab/10-bonus-sdd-conductor.md) · **Needs:** theory 02 (harness), 04 (loops), and 06 (plugins)

So far you asked Claude to build features directly from prompts or user stories. In small scripts this works. In production codebases, prompting without strict guardrails creates **Intent Debt** (the agent invents design decisions because the prompt was ambiguous) and **Comprehension Debt** (code accumulates faster than your team can understand it).

The industry antidote is **Spec-Driven Development (SDD)**: *"Measure twice, code once."*

---

## 1. What is Spec-Driven Development?

Instead of jumping straight to code, SDD splits the engineering process into three distinct, gated phases:

```
┌─────────────────┐      ┌─────────────────────────┐      ┌─────────────────────────┐
│ 1. CONTEXT      │ ──►  │ 2. SPEC & PLAN          │ ──►  │ 3. IMPLEMENTATION LOOP  │
│ product.md      │      │ spec.md  (What & Why)   │      │ Task-by-task execution  │
│ tech-stack.md   │      │ plan.md  (Phases & TDD) │      │ Checked against plan    │
│ workflow.md     │      │ metadata.json           │      │ Real-time status update │
└─────────────────┘      └─────────────────────────┘      └─────────────────────────┘
```

| Phase | Purpose | Artifacts |
|---|---|---|
| **Context** | Single source of truth for the codebase and team conventions. | `conductor/product.md`, `conductor/tech-stack.md`, `conductor/workflow.md` |
| **Spec & Plan** | Formal contract for a specific unit of work (a "Track") before writing code. | `conductor/tracks/<id>/spec.md`, `conductor/tracks/<id>/plan.md` |
| **Implement** | Deterministic closed-loop execution. Tasks checked off as they pass tests. | `conductor/tracks.md`, code changes, passing test suite |

---

## 2. Introducing Conductor (Google)

**Conductor** (`gemini-cli-extensions/conductor`) is an open-source plugin built by Google for AI coding agents (Claude Code and Antigravity). It turns your agent into an architectural project manager that enforces the SDD protocol.

Conductor provides structured slash commands:

- `/conductor:conductor-setup` — Audits the repo (Brownfield or Greenfield) and scaffolds shared context.
- `/conductor:conductor-new-track` — Conducts a discovery interview to write a verifiable `spec.md` and an atomic, phase-based `plan.md`.
- `/conductor:conductor-implement` — Executes the tasks in `plan.md` sequentially, verifying each checkpoint.
- `/conductor:conductor-status` — Shows track progress, active phases, and pending work.
- `/conductor:conductor-review` — Acts as a Principal Engineer reviewing code diffs against the original spec.

---

## 3. Why SDD beats "Vibe Coding"

```
Freeform ("Vibe Coding"):
Prompt ──► Code generated ──► Broken edge cases ──► Prompt to fix ──► Regressions

Spec-Driven Development:
Context ──► Spec (approved) ──► Plan (approved) ──► Small verified diffs ──► Green
```

1. **Catches mistakes when they are cheapest:** A flaw spotted in `spec.md` takes 10 seconds to fix. The same flaw spotted after 300 lines of generated code requires rollbacks and debugging.
2. **Deterministic execution:** The agent does not wander or attempt out-of-scope refactors because `plan.md` strictly bounds what it is allowed to touch.
3. **Auditability:** Team members can review the track's `spec.md` and `plan.md` in Git pull requests, keeping comprehension high.

---

## Check yourself
1. What is the difference between `product.md` (project level) and `spec.md` (track level)?
2. Why is reviewing `plan.md` *before* running `/conductor:conductor-implement` essential?
3. How does SDD prevent "Intent Debt"?

**Next:** [Lab 10 (Bonus) — Spec-Driven Development with Conductor](../lab/10-bonus-sdd-conductor.md)
