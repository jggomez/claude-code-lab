# TaskBoard — Tech Stack

Keep it boring and small. Fewer moving parts means more time learning Claude Code.

## Frontend
- **HTML5** — single page, `public/index.html`
- **CSS** — minimal custom CSS in `public/styles.css` (only what Tailwind can't do)
- **Tailwind CSS** — via CDN (`<script src="https://cdn.tailwindcss.com"></script>`), **no build step**
- **JavaScript** — vanilla ES modules in `public/app.js`; use `fetch`, no frameworks

## Backend
- **Node.js** 20+ (CommonJS or ESM, pick one and stay consistent)
- **Express** — serves `public/` as static files and exposes the REST API under `/api`
- **No database** — an in-memory array; resets on restart

## Testing
- Node's built-in runner: `node --test`
- API tests with `fetch` against the app instance (export `app` from `src/app.js`, start the server in `src/server.js`)
- Do not add Jest/Vitest.

## Project layout
```
taskboard/
├── package.json
├── src/
│   ├── app.js          # Express app (exported, no listen)
│   ├── server.js       # starts the server on PORT || 3000
│   └── tasks.js        # in-memory store + business logic
├── public/
│   ├── index.html
│   ├── styles.css
│   └── app.js
└── test/
    └── tasks.test.js
```

## Scripts
| Command | Purpose |
|---|---|
| `npm start` | run the server on port 3000 |
| `npm run dev` | `node --watch src/server.js` |
| `npm test` | `node --test` |

## Conventions
- 2-space indentation, semicolons, single quotes.
- Small functions, no dead code, no unnecessary dependencies.
- Only dependency allowed: `express`.
- Errors return JSON: `{ "error": "message" }`.
