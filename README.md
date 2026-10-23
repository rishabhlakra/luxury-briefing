# Luxury Briefing

Luxury Briefing is a small full-stack application I built to collect and review luxury-market news and events.

[![CI](https://github.com/rishabhlakra/luxury-briefing/actions/workflows/ci.yml/badge.svg)](https://github.com/rishabhlakra/luxury-briefing/actions/workflows/ci.yml)

## What it does

- reads articles from a set of RSS feeds
- normalizes and stores them in PostgreSQL
- marks items with a theme, confidence score, rarity label, and freshness status
- supports text search, filtering, and sorting
- lets a user save items to a personal vault
- shows upcoming auction and event windows

The confidence and rarity values are rule-based. They are meant to help organize a feed, not predict the market.

## How it is built

```text
RSS feeds and event sources
            |
            v
Python ingestion -> PostgreSQL -> FastAPI -> React / TypeScript interface
```

- **Frontend:** React, TypeScript, Vite, CSS
- **Backend:** Python, FastAPI, SQLAlchemy
- **Database:** PostgreSQL
- **Local setup:** Docker Compose
- **Checks:** ESLint, TypeScript build, Python compilation, GitHub Actions

Repeated ingestion updates existing records by signal ID or source URL instead of inserting another copy. Search, filters, and sorting are handled by the API. Saved items use a separate relational table and idempotent save/remove endpoints.

## Run it locally

The simplest option is Docker Compose:

```bash
git clone https://github.com/rishabhlakra/luxury-briefing.git
cd luxury-briefing
docker compose up --build
```

The interface will be available at `http://127.0.0.1:5173` once the services are running.

To run the applications separately, start PostgreSQL first:

```bash
docker compose up -d db
```

Then start the API:

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

In another terminal, start the frontend:

```bash
cd frontend
npm install
npm run dev
```

Set `VITE_API_BASE` if the API is not running at `http://127.0.0.1:8000`.

## API routes

| Method | Route | Use |
|---|---|---|
| `GET` | `/health` | Health check |
| `GET` | `/themes` | List themes |
| `GET` | `/signals` | Search, filter, and sort signals |
| `GET` | `/saved` | List saved signals |
| `POST` | `/save/{signal_id}` | Save a signal |
| `DELETE` | `/save/{signal_id}` | Remove a saved signal |
| `POST` | `/ingest` | Refresh RSS data |
| `GET` | `/events` | List upcoming events |
| `GET` | `/events/curated` | List curated event windows |

## Project layout

```text
backend/           FastAPI application, database models, and ingestion
frontend/          React and TypeScript application
.github/workflows  CI checks
docker-compose.yml Local PostgreSQL, API, and frontend services
```
