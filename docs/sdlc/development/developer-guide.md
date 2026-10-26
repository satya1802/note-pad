# Developer Guide

> Maintained by QuorQ AI SDLC. Text between `sdlc:auto` markers is regenerated; anything outside them is yours and is kept.

<!-- sdlc:auto:start status -->
_Up to date._
<!-- sdlc:auto:end status -->

<!-- sdlc:auto:start source -->
Generated from commit `590a4c5` · model `claude-sonnet-5`
<!-- sdlc:auto:end source -->

<!-- sdlc:auto:start overview -->
note pad is a generated two-application repository from the Quorq AI SDLC platform: a frontend (Vite + React 19 SPA) containing the approved, reviewed screens running on seeded sample data, and a backend (FastAPI 0.115) that currently has only a health check endpoint since no backend architecture had been approved yet. The backend uses SQLAlchemy with a SQLite database by default (swappable via DATABASE_URL, with Postgres 16 named as the approved target), and the frontend is wired via a typed API client (src/lib/api.ts) that is not yet used by any screen. Docker Compose provides a Postgres container for local development, and the backend has its own Dockerfile for containerized runs.
<!-- sdlc:auto:end overview -->

<!-- sdlc:auto:start prerequisites -->
- Node.js (for Vite 6 / React 19 / TypeScript 5.6 tooling)
- Python 3.12
- pip
- Docker and Docker Compose (optional, for Postgres and backend container)
<!-- sdlc:auto:end prerequisites -->

<!-- sdlc:auto:start setup -->
1. Clone the repository
2. Copy .env.example to .env and fill in values as needed
3. cd frontend && npm install
4. cd backend && pip install -r requirements.txt
5. (Optional) docker compose up -d to start the Postgres 16 container defined in docker-compose.yml
6. (Optional) set DATABASE_URL to point the backend at Postgres instead of the default SQLite file
<!-- sdlc:auto:end setup -->

<!-- sdlc:auto:start run -->
| Name | Command | Description |
|---|---|---|
| frontend dev | `cd frontend && npm run dev` | Starts the Vite dev server for the React SPA |
| frontend build | `cd frontend && npm run build` | Bundles the app with Vite (does not typecheck) |
| frontend preview | `cd frontend && npm run preview` | Serves the production build locally |
| backend dev | `cd backend && uvicorn app.main:app --reload` | Runs the FastAPI app with auto-reload; creates SQLite tables on startup and serves GET /health |
| compose db | `docker compose up -d` | Starts the Postgres 16 container defined in docker-compose.yml on port 5432 |
| backend docker build | `docker build -t note-pad-backend ./backend` | Builds the backend container image from backend/Dockerfile |
<!-- sdlc:auto:end run -->

<!-- sdlc:auto:start structure -->
| Path | Purpose |
|---|---|
| `frontend` | React 19 + Vite SPA with approved screens, running on seeded sample data |
| `frontend/src` | Application source; includes lib/ (api client, UI/icon vocabulary, navigation), main.tsx entrypoint, index.css brand tokens |
| `frontend/e2e` | Playwright acceptance tests |
| `backend` | FastAPI service, currently only exposing a health check |
| `backend/app` | Application package: main.py (entrypoint/CORS/startup), database.py (engine/session), models.py (SQLAlchemy models, empty scaffold), schemas.py (Pydantic schemas), routers/ (per-component routers, currently empty) |
| `backend/tests` | Pytest tests (test_health.py) |
| `.quorq` | Platform handoff metadata (handoff.json) |
<!-- sdlc:auto:end structure -->

<!-- sdlc:auto:start conventions -->
- Backend: one router per approved architecture component, one route per spec'd endpoint; unimplemented routes return a typed StubResponse rather than raising, keeping the service startable with a complete OpenAPI document
- Backend: Base (SQLAlchemy declarative base) is defined once in database.py and imported by models.py; main.py imports models before calling Base.metadata.create_all so tables register
- Backend: environment-driven configuration (DATABASE_URL, ALLOWED_ORIGINS) with safe local defaults (SQLite, localhost CORS origins)
- Frontend: one module per approved screen under src/screens/, with App.tsx as the navigation shell/route table
- Frontend: shared UI/icon vocabulary in src/lib/ui.tsx and src/lib/icons.tsx; brand tokens in src/lib/brand.ts and index.css
- Frontend: component tests live beside the components they test; Vitest only collects src/**/*.{test,spec}.{ts,tsx}, leaving e2e/*.spec.ts to Playwright
- Frontend: API base URL and secret key are injected at build time via VITE_API_BASE_URL / VITE_API_SECRET_KEY env vars, consumed in src/lib/api.ts
- Containers run as an unprivileged user (backend Dockerfile creates and switches to appuser)
<!-- sdlc:auto:end conventions -->

<!-- sdlc:auto:start testing -->
| Name | Command | Description |
|---|---|---|
| backend tests | `cd backend && pytest` | Runs backend unit tests (test_health.py) using pytest with pythonpath=. so `import app` resolves |
| backend lint | `cd backend && ruff check .` | Lints the backend Python code per ruff.toml |
| backend format | `cd backend && ruff format .` | Formats backend Python code |
| frontend unit tests | `cd frontend && npm test` | Runs Vitest component tests (passWithNoTests) |
| frontend typecheck | `cd frontend && npm run typecheck` | Runs tsc --noEmit; not run by npm run build |
| frontend lint | `cd frontend && npm run lint` | Runs ESLint over the whole app; this is the gate the platform runs during development |
| frontend e2e install | `cd frontend && npm run test:e2e:install` | Installs the Chromium browser for Playwright |
| frontend e2e | `cd frontend && npm run test:e2e` | Runs Playwright acceptance tests from e2e/; without E2E_BASE_URL it boots the backend (uvicorn) and the built/previewed frontend itself |
<!-- sdlc:auto:end testing -->

<!-- sdlc:auto:start troubleshooting -->
| Problem | Solution |
|---|---|
| `pytest` run from backend/ fails with `ModuleNotFoundError: No module named 'app'` | Ensure you run pytest from the backend/ directory where pytest.ini sets pythonpath = . so `import app` resolves |
| Frontend build succeeds but deployment/tsc fails later | `npm run build` does not typecheck; run `npm run typecheck` (tsc --noEmit) separately and fix errors before relying on a clean build |
| Browser blocks API calls from the frontend with a CORS error | Set the ALLOWED_ORIGINS env var (comma-separated) on the backend to include the frontend's origin; defaults only allow http://localhost:5173 and http://127.0.0.1:5173 |
| Playwright e2e tests fail to start the backend | The webServer config runs `pip install -q -r requirements.txt` and starts uvicorn automatically from ../backend when E2E_BASE_URL is not set; ensure Python/pip is available on PATH, or set E2E_BASE_URL to point at an already-running deployment |
| Need a persistent/production-like database instead of the default SQLite file | Start Postgres via `docker compose up -d` and set DATABASE_URL to the Postgres connection string; no code changes are needed since database.py reads DATABASE_URL |
<!-- sdlc:auto:end troubleshooting -->
