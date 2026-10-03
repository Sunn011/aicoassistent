# AICO Assistant — Backend

Backend package for the **AICO Assistant** web application.

The original backend functionality is preserved: client requests can be received as mail, split into tasks, assigned using employee skills/seniority/workload, tracked through progress, escalated when blockers occur, and followed by a completion summary. fileciteturn0file2L5-L11

## Updated for the current website

- Frontend URL: `https://aicoassistent.netlify.app`
- Backend branding: **AICO Assistant**
- Render service name: `aico-assistant-backend`
- CORS is enabled for the configured `APP_URL`
- API `OPTIONS` requests are supported for browser preflight
- Health response identifies the app as `AICO Assistant`

The old package used a Node HTTP server with JSON API routes and a static `/public` frontend. fileciteturn0file0L2-L12 This package keeps that API architecture so the existing `src/` implementation can be dropped into it.

## Required project structure

Keep the existing implementation directories/files from your old project:

```text
aico-assistant/
├── server.js
├── package.json
├── render.yaml
├── Dockerfile
├── .env.example
├── .gitignore
├── src/
│   ├── config.js
│   ├── events.js
│   ├── service.js
│   ├── inbox.js
│   ├── store.js
│   ├── seed.js
│   ├── planner.js
│   ├── assign.js
│   └── mailer.js
├── public/
│   └── ...your current frontend files...
└── test/
    └── smoke.test.js
```

The previous project documented the same core modules for configuration, storage, planning, assignment, service logic, mail and inbox handling. fileciteturn0file2L16-L25

## Run locally

Requires Node.js 18+.

```bash
npm install
cp .env.example .env
npm start
```

Open:

```text
http://localhost:3000
```

Test:

```bash
npm test
```

## Connect the Netlify frontend

Set this in `.env`:

```env
APP_URL=https://aicoassistent.netlify.app
```

Your Netlify frontend must call the **backend URL**, not the Netlify URL, for API requests.

For example:

```js
const API_BASE_URL = "https://YOUR-RENDER-BACKEND.onrender.com";
```

Then:

```js
fetch(`${API_BASE_URL}/api/state`)
```

Do not put SMTP, IMAP, Anthropic or other private secrets in the Netlify frontend.

## API routes retained

| Method | Route | Purpose |
|---|---|---|
| GET | `/api/health` | Backend health |
| GET | `/api/state` | Current employees/projects/tasks/outbox/notifications/activity |
| GET | `/api/events` | Live SSE events |
| POST | `/api/mails` | Create a project from client mail |
| POST | `/api/tasks/:id/start` | Start a task |
| POST | `/api/tasks/:id/progress` | Update progress |
| POST | `/api/tasks/:id/complete` | Complete a task |
| POST | `/api/tasks/:id/blocker` | Raise a blocker |
| POST | `/api/tasks/:id/resolve` | Resolve a blocker |
| POST | `/api/tasks/:id/reassign` | Reassign a task |
| POST | `/api/tasks/:id/nudge` | Nudge an assignee |
| POST | `/api/notifications/read` | Mark notifications read |
| POST | `/api/reset` | Reset demo state |

These routes correspond to the original documented API. fileciteturn0file2L56-L71

## Environment variables

```env
PORT=3000
APP_URL=https://aicoassistent.netlify.app
DATA_FILE=./data/db.json

COMPANY_NAME=AICO Assistant
DELIVERY_EMAIL=delivery@yourcompany.com

TEAM_LEAD_NAME=
TEAM_LEAD_EMAIL=
ADMIN_EMAIL=

SMTP_HOST=
SMTP_PORT=465
SMTP_SECURE=true
SMTP_USER=
SMTP_PASS=
SMTP_FROM="AICO Assistant <delivery@yourcompany.com>"

IMAP_HOST=
IMAP_PORT=993
IMAP_USER=
IMAP_PASS=
IMAP_POLL_SECONDS=60

ANTHROPIC_API_KEY=
ANTHROPIC_MODEL=claude-sonnet-5-5
```

Without SMTP configuration, the old system runs in dry-run mode and logs outgoing mail rather than sending it. fileciteturn0file2L41-L49

## Render deployment

The updated `render.yaml` is configured for:

- Node runtime
- `npm install`
- `npm start`
- `/api/health` health check
- persistent `/var/data/db.json`
- `APP_URL=https://aicoassistent.netlify.app`

The previous deployment configuration used the same Render build/start/health-check pattern and persistent data mount. fileciteturn0file1L2-L16

## Important

The files supplied in this chat are backend/deployment files. They do **not** include the current Netlify frontend source (`public/`, React/Vite source, etc.). I therefore did not invent or overwrite the current AICO Assistant UI.

If you want the downloaded package to contain the **exact current website source**, upload the current frontend/source files (or the current GitHub repository ZIP) as well. Then the frontend can be wired to this backend without guessing.

## Security

Before exposing the backend publicly, add authentication/authorization. The original project explicitly noted that its endpoints had no authentication and that the browser's employee selector was only a demo switch. fileciteturn0file2L116-L121
