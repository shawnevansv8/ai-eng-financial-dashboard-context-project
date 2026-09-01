# Verification Trail

## Phase 1 — Handover Verification

- **Frontend:** React 19 with TypeScript, built and served with Vite. The HTML entry point loads `src/main.tsx`, which mounts the dashboard application. Evidence: [frontend/package.json](frontend/package.json), [frontend/index.html](frontend/index.html), [frontend/src/main.tsx](frontend/src/main.tsx).
- **Backend:** Python FastAPI application with the `app.main:app` ASGI entry point; Uvicorn and debugpy are runtime dependencies. Evidence: [backend/app/main.py](backend/app/main.py), [backend/requirements.txt](backend/requirements.txt), [backend/Dockerfile](backend/Dockerfile).
- **Docker Compose:** Defines `frontend` and `backend` services. It publishes frontend port 5173, backend port 8000, and backend debugpy port 5678. Evidence: [docker-compose.yml](docker-compose.yml).
- **Project purpose:** Financial metrics dashboard showing total income, total outcome, profit, profit margin, monthly income-versus-outcome, and profit-margin charts. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/src/components/dashboard/kpi-row.tsx](frontend/src/components/dashboard/kpi-row.tsx), [frontend/src/components/dashboard/income-outcome-chart.tsx](frontend/src/components/dashboard/income-outcome-chart.tsx), [frontend/src/components/dashboard/profit-percent-chart.tsx](frontend/src/components/dashboard/profit-percent-chart.tsx).
- **Frontend/backend communication:** The frontend uses native `fetch` to request `/api/metrics`. `VITE_API_BASE_URL` optionally prefixes the request; without it, Vite proxies `/api` to `http://backend:8000`. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/vite.config.ts](frontend/vite.config.ts), [frontend/.env.example](frontend/.env.example).
- **API:** The backend implements `GET /health` and these metrics endpoints: `GET /api/metrics`, `/api/metrics/facets`, `/api/metrics/summary`, `/api/metrics/categories/top`, `/api/metrics/comparison`, `/api/metrics/alerts`, `/api/metrics/b2b`, and `/api/metrics/b2c`. Evidence: [backend/app/routes.py](backend/app/routes.py).

## Runtime Verification

- **Startup command:** `docker compose up --build` completed successfully.
- **Frontend status:** Running in the `frontend` Compose container; actual published mapping: `0.0.0.0:5173->5173/tcp` and `[::]:5173->5173/tcp`.
- **Backend status:** Running in the `backend` Compose container; actual published mappings: `0.0.0.0:8000->8000/tcp`, `[::]:8000->8000/tcp`, `0.0.0.0:5678->5678/tcp`, and `[::]:5678->5678/tcp`.
- **Frontend URL:** `http://localhost:5173/` returned HTTP 200, `Content-Type: text/html`, and a 615-byte response.
- **Backend URL:** `http://localhost:8000/` is the published backend origin.
- **API docs URL:** `http://localhost:8000/docs` returned HTTP 200, `Content-Type: text/html; charset=utf-8`, and a 1,020-byte response.
- **Health endpoint result:** `GET http://localhost:8000/health` returned HTTP 200 with `{"status":"ok"}`.
- **`/api/metrics` result:** `GET http://localhost:8000/api/metrics` returned HTTP 200, `Content-Type: application/json`, and a 41,683-byte response.
- **Frontend `/api/metrics` proxy result:** `GET http://localhost:5173/api/metrics` returned HTTP 200, `Content-Type: application/json`, and a 41,683-byte response.
- **Frontend tests:** `docker compose exec -T frontend npm test` completed successfully: 5/5 tests passed.
- **Backend tests:** `docker compose exec -T backend env PYTHONDONTWRITEBYTECODE=1 pytest -p no:cacheprovider` completed successfully: 15/15 tests passed. Pytest emitted one FastAPI/TestClient deprecation warning but no test failures.

## Corrections

The initial file search suggested that `frontend/.env.example` was absent. A later source search confirmed it exists and defines `VITE_API_BASE_URL=`. Evidence: [frontend/.env.example](frontend/.env.example).

## Unverified / Unknown

- Business users are not defined by the inspected repository files.
- No authentication model is defined by the inspected repository files.
- No persistent data storage is configured; the backend generates in-memory mock movements. Evidence: [backend/app/routes.py](backend/app/routes.py).
- No production deployment process is defined by the inspected repository files.
- Docker Compose and the frontend do not define health-check configuration. Evidence: [docker-compose.yml](docker-compose.yml), [frontend/package.json](frontend/package.json).

## Evidence Standard

Important claims in this trail were verified against repository configuration and source files, then confirmed where applicable by running the documented Compose startup command, inspecting live containers and published ports, requesting live HTTP endpoints, and executing the existing test suites. No conclusions are based on assumptions.