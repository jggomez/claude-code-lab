# Lab 10 (Bonus) — Spec-Driven Development with Conductor (Google)

**Time:** 15–20 min · **Theory:** [08 — Spec-Driven Development](../theory/08-spec-driven-development.md)
**Needs:** Labs 01 (basics), 02 (harness), 05 (loops), and 07 (plugins).

## Goal
Experience **Spec-Driven Development (SDD)** with Google's **Conductor** plugin. Instead of freeform prompt-and-pray, use a structured protocol to build a new feature — **"Clear completed tasks"** — with zero architectural drift:

```
/conductor:conductor-setup      Scaffolds project context (Brownfield scan)
            │
/conductor:conductor-new-track  Generates spec.md & actionable plan.md
            │
      [You Review & Approve]    Gatekeeper: No code written until approved
            │
/conductor:conductor-implement  Closed-loop task execution against plan.md
```

---

## Part 1 — Install the Conductor plugin (3 min)

Conductor is distributed through the `gemini-cli-extensions/conductor` marketplace.

### 1.1 Register and install
Inside your `taskboard/` session in Claude Code:
```
/plugin marketplace add gemini-cli-extensions/conductor
/plugin install conductor@conductor
```
> **Alternative (offline / local):** If running in an environment without direct GitHub marketplace access, link or load Conductor locally:
> `claude --plugin-dir ~/.gemini/config/plugins/conductor`

### 1.2 Verify installation
Check that Conductor is active:
```
/plugin
```
Type `/conductor:` — you should see the available slash commands:
- `/conductor:conductor-setup`
- `/conductor:conductor-new-track`
- `/conductor:conductor-implement`
- `/conductor:conductor-status`
- `/conductor:conductor-review`

---

## Part 2 — Scaffold project context with `/conductor:conductor-setup` (4 min)

Before writing any new feature, Conductor establishes the **foundational context** of your repository.

Inside Claude Code:
```
/conductor:conductor-setup
```

### What to do during setup:
1. **Audit scan:** Conductor detects a **Brownfield** project (identifying `package.json` and `src/`). Reply **Yes** to allow the read-only scan.
2. **Context generation:** Conductor infers your stack (Node.js, Express, Vanilla JS, in-memory data). When asked for creation mode, choose **Autogenerate** (or approve the defaults).
3. **Artifact creation:** Conductor creates a clean `conductor/` directory containing:
   - `conductor/product.md` — Vision and user persona for TaskBoard.
   - `conductor/tech-stack.md` — Inferred tech stack and constraints.
   - `conductor/workflow.md` — Development workflow standards.
   - `conductor/tracks.md` — The track registry tracking progress.
   - `conductor/index.md` — The central index linking all context files.

---

## Part 3 — Plan the feature track with `/conductor:conductor-new-track` (5 min)

Now add the new feature: **Clear all completed tasks with a single click**.

### 3.1 Start a new track
Inside Claude Code:
```
/conductor:conductor-new-track "Clear all completed tasks"
```

### 3.2 Review the generated specification (`spec.md`)
Conductor asks 1–2 clarifying questions (or auto-proposes the spec). Confirm:
- **Backend contract:** `POST /api/tasks/clear-completed` removes all tasks where `done === true` and returns `{ "deletedCount": number }`.
- **Frontend contract:** A "Clear completed" button appears in the action bar, disabled or hidden when there are 0 completed tasks.
- **Edge cases:** Calling it when no tasks are completed returns `200` with `{ "deletedCount": 0 }`.

When Conductor presents the drafted specification, select **Approve**.

### 3.3 Review the execution plan (`plan.md`)
Conductor translates the specification into atomic phases:
1. **Phase 1: Backend & Tests** — Add `POST /api/tasks/clear-completed` in `src/index.js` and automated tests in `test/api.test.js`.
2. **Phase 2: Frontend UI** — Add the "Clear completed" button in `public/index.html` and click handler in `public/app.js`.
3. **Phase 3: Verification** — Run `npm test` and verify end-to-end.

Select **Approve** to lock the plan.

---

## Part 4 — Execute with `/conductor:conductor-implement` (5 min)

With the plan approved, launch the deterministic implementation loop:

```
/conductor:conductor-implement
```

### Observe:
- Claude Code reads `conductor/tracks/<track_id>/plan.md`.
- It executes **one task at a time**, marking each checkbox `- [x]` as it completes.
- It runs `npm test` automatically to verify that existing features (HU1–HU6) still pass and new tests succeed.
- Once finished, it updates `conductor/tracks.md` status to `Completed`.

---

## Part 5 — Check status and review (2 min)

### 5.1 Check track status
```
/conductor:conductor-status
```
You will see the track marked as complete with all tasks ticked.

### 5.2 Verify in browser and tests
From your shell:
```bash
npm test
npm start
```
Open `http://localhost:3000`:
1. Add two tasks and mark one as completed.
2. Click **"Clear completed"**.
3. Observe that only the completed task is removed immediately without full page reload.

---

## Checkpoint
- [ ] Conductor plugin is installed and slash commands appear under `/conductor:`
- [ ] `conductor/` context files (`product.md`, `tech-stack.md`, `workflow.md`) exist
- [ ] Track artifacts exist under `conductor/tracks/` with an approved `spec.md` and `plan.md`
- [ ] All tests in `npm test` pass
- [ ] "Clear completed" works in the browser

## What to observe
- **Zero wandering:** Because `plan.md` defined exact sub-tasks, Claude Code didn't rewrite unrelated files or invent unrequested abstractions.
- **Traceability:** You have an auditable paper trail in Git (`spec.md` + `plan.md`) explaining *why* the code was written this way, eliminating Intent Debt.

## If something fails
- **Marketplace command not found:** Ensure you are running Claude Code v0.2.20+ with plugin support enabled.
- **Conductor setup stalls on questions:** Type `Autogenerate` or `Accept defaults` to quickly accept the inferred stack.
- **Test fails:** Check `test/api.test.js`; make sure the endpoint matches `POST /api/tasks/clear-completed`.
