# AI Email Assistant

Production-ready AI-powered email client with Gmail integration, multi-provider AI, real-time sync, and a modern React UI.

## Features

- **Google OAuth 2.0** with JWT sessions and optional MFA (TOTP)
- **Gmail API** — read, send, labels, attachments, threads, drafts, background sync
- **AI** — summaries, smart compose, rewrite (formal/casual/shorter/longer), mailbox chat (OpenAI / Anthropic / Gemini)
- **Mail UI** — inbox, sent, drafts, spam, trash, archive, starred, search, infinite scroll
- **Rich compose** — TipTap editor, AI compose, undo-send buffer
- **Dashboard** — analytics and AI usage stats
- **Real-time** — WebSocket notifications
- **Settings** — theme, AI provider, signatures, workflow automations
- **Keyboard shortcuts** — `Cmd/Ctrl+K` command palette, `Cmd/Ctrl+N` compose

## Tech Stack

| Layer | Stack |
|-------|-------|
| Frontend | React, TypeScript, Vite, TanStack Router, TanStack Query, Tailwind CSS, shadcn/ui, Framer Motion, TipTap |
| Backend | FastAPI, SQLAlchemy, PostgreSQL, Alembic, Redis, Celery |
| Auth | Google OAuth 2.0, JWT, Refresh Token Rotation |
| AI | OpenAI, Anthropic, Gemini (provider abstraction) |

## Quick Start (Docker)

```bash
cp .env.example .env
# Edit .env with GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET, and AI keys

docker compose up
```

- Frontend: http://localhost:5173
- Backend API: http://localhost:8000
- API Docs: http://localhost:8000/docs

## Local Development

### Prerequisites

- Node.js 20+
- Python 3.12+
- PostgreSQL 16
- Redis 7

### Backend

```bash
cd backend
python -m venv venv
# Windows: venv\Scripts\activate
# macOS/Linux: source venv/bin/activate
pip install -r requirements.txt

# Start Postgres & Redis, then:
cp ../.env.example .env
alembic upgrade head
uvicorn app.main:app --reload
```

### Celery (background sync)

```bash
cd backend
celery -A app.workers.celery worker --loglevel=info
celery -A app.workers.celery beat --loglevel=info
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

## Google OAuth Setup

1. Create a project in [Google Cloud Console](https://console.cloud.google.com/)
2. Enable **Gmail API** and **Google People API**
3. Create OAuth 2.0 credentials (Web application)
4. Add authorized redirect URI: `http://localhost:8000/api/v1/auth/google/callback`
5. Copy Client ID and Secret to `.env`

## Environment Variables

See `.env.example` for all options. Required for full functionality:

| Variable | Description |
|----------|-------------|
| `GOOGLE_CLIENT_ID` | Google OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | Google OAuth client secret |
| `SECRET_KEY` | JWT signing key |
| `ENCRYPTION_KEY` | 32-byte key for token encryption |
| `DATABASE_URL` | PostgreSQL connection string |
| `REDIS_URL` | Redis connection string |
| `OPENAI_API_KEY` | OpenAI API key (or Gemini/Anthropic) |

## API Endpoints

Base URL: `/api/v1`

| Group | Endpoints |
|-------|-----------|
| Auth | `/auth/google/login`, `/auth/google/callback`, `/auth/me`, `/auth/refresh`, MFA, sessions |
| Mail | `/mail`, `/mail/{id}`, `/mail/send`, `/mail/sync`, `/mail/labels`, actions, attachments |
| AI | `/ai/action` (SSE), `/ai/chat` (SSE), `/ai/prompts` |
| Settings | `/settings`, templates, workflows |
| Analytics | `/analytics`, `/analytics/export` |
| Notifications | `/notifications` |
| WebSocket | `/ws?token=<jwt>` |

## Tests

```bash
cd backend
pytest tests/ -v
```

## Deployment

### Frontend (Vercel)

```bash
cd frontend
npm run build
```

Deploy with `vercel.json` — set `VITE_API_URL` to your backend URL.

### Backend (Render / Railway)

Use `render.yaml` or deploy the Docker image. Set all environment variables from `.env.example`.

## Project Structure

```
ai-email-assistant/
├── backend/
│   ├── app/
│   │   ├── api/v1/          # REST endpoints
│   │   ├── auth/            # OAuth, MFA, RTR
│   │   ├── models/          # SQLAlchemy models
│   │   ├── services/        # Gmail, AI
│   │   └── workers/         # Celery tasks
│   ├── alembic/             # Migrations
│   └── tests/
├── frontend/
│   └── src/
│       ├── components/      # UI components
│       ├── pages/           # Route pages
│       └── lib/             # API client, types
├── docker-compose.yml
└── README.md
```

## License

MIT
