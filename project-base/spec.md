# TaskBoard — Product Spec

A tiny task manager. One page, one API. The goal of this project is **not** the app itself — it is a vehicle to practice Claude Code.

## Goal
Let a user manage a simple list of tasks from the browser.

## Users
A single anonymous user. No login, no accounts.

## User Stories

### HU1 — View tasks
**As a** user, **I want** to see my list of tasks **so that** I know what I have to do.

Acceptance criteria:
- On load, the page fetches `GET /api/tasks` and renders every task.
- Each task shows its title and whether it is done.
- If there are no tasks, the page shows "No tasks yet".

### HU2 — Create a task
**As a** user, **I want** to add a task with a title **so that** I can track new work.

Acceptance criteria:
- A form with a text input and an "Add" button.
- Empty or whitespace-only titles are rejected (API returns `400`, UI shows an error).
- The new task appears in the list without reloading the page.

### HU3 — Complete a task
**As a** user, **I want** to mark a task as done (and undo it) **so that** I can track progress.

Acceptance criteria:
- Clicking a checkbox toggles `done` through `PATCH /api/tasks/:id`.
- Done tasks are visually distinct (strikethrough, muted color).
- Unknown id returns `404`.

### HU4 — Delete a task
**As a** user, **I want** to delete a task **so that** my list stays clean.

Acceptance criteria:
- Each task has a delete button calling `DELETE /api/tasks/:id`.
- The task disappears from the list immediately.
- Unknown id returns `404`.

### HU5 — Filter tasks
**As a** user, **I want** to filter by All / Pending / Done **so that** I can focus.

Acceptance criteria:
- Three filter buttons; the active one is highlighted.
- Filtering can be done client-side or with `GET /api/tasks?status=pending|done`.
- The filter survives adding/completing/deleting a task.

### HU6 — Priority and counter *(stretch)*
**As a** user, **I want** to set a priority (low / normal / high) and see how many tasks are pending **so that** I can plan.

Acceptance criteria:
- Priority is chosen on creation and shown as a colored badge.
- A counter shows "N pending".

## API contract

| Method | Path | Body | Success | Errors |
|---|---|---|---|---|
| GET | `/api/tasks` | — | `200` array of tasks | — |
| POST | `/api/tasks` | `{ "title": string }` | `201` created task | `400` invalid title |
| PATCH | `/api/tasks/:id` | `{ "done": boolean }` | `200` updated task | `404` not found |
| DELETE | `/api/tasks/:id` | — | `204` | `404` not found |

Task shape: `{ "id": number, "title": string, "done": boolean }`

## Non-functional requirements
- No database: data lives in memory and resets on restart.
- Works in the latest Chrome/Firefox, mobile-friendly layout.
- Everything runs with `npm install && npm start` on `http://localhost:3000`.

## Out of scope
Authentication, persistence, multi-user, deployment.
