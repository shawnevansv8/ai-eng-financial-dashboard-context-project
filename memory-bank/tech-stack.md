# Technology Stack

## Frontend

- **Framework and language:** React 19 with TypeScript. Evidence: [frontend/package.json](frontend/package.json), [frontend/src/main.tsx](frontend/src/main.tsx), [frontend/tsconfig.app.json](frontend/tsconfig.app.json).
- **Build and development:** Vite provides the development server, production build, and preview commands. Evidence: [frontend/package.json](frontend/package.json), [frontend/vite.config.ts](frontend/vite.config.ts).
- **Styling and UI:** Tailwind CSS is enabled through the Vite plugin; the project uses reusable UI primitives, Lucide React icons, and Recharts visualizations. Evidence: [frontend/package.json](frontend/package.json), [frontend/vite.config.ts](frontend/vite.config.ts), [frontend/src/components/ui/card.tsx](frontend/src/components/ui/card.tsx), [frontend/src/components/dashboard/income-outcome-chart.tsx](frontend/src/components/dashboard/income-outcome-chart.tsx).
- **TypeScript configuration:** The app uses the `@/` source alias, React JSX, no emit, and unused-code checks. Evidence: [frontend/tsconfig.app.json](frontend/tsconfig.app.json).

## Backend

- **Framework and language:** Python FastAPI application. Evidence: [backend/app/main.py](backend/app/main.py), [backend/requirements.txt](backend/requirements.txt).
- **Server and debugging:** Uvicorn serves `app.main:app`; debugpy is part of the container command. Evidence: [backend/Dockerfile](backend/Dockerfile), [backend/requirements.txt](backend/requirements.txt).
- **Data models:** FastAPI routes use Pydantic `BaseModel` response schemas and Python `Literal` domain types. Evidence: [backend/app/routes.py](backend/app/routes.py).

## Infrastructure

- **Docker Compose:** `docker compose up --build` starts `frontend` and `backend`; frontend depends on backend. Evidence: [README.md](README.md), [docker-compose.yml](docker-compose.yml).
- **Ports:** Compose publishes frontend `5173:5173`, backend `8000:8000`, and debugpy `5678:5678`. Evidence: [docker-compose.yml](docker-compose.yml). Runtime verification confirmed these live host mappings. Evidence: [verification.md](verification.md).
- **Container commands:** The frontend container runs Vite on `0.0.0.0:5173`; the backend container runs debugpy and Uvicorn on `0.0.0.0:8000`. Evidence: [frontend/Dockerfile](frontend/Dockerfile), [backend/Dockerfile](backend/Dockerfile).

## Testing

- **Frontend:** Vitest runs the colocated utility tests through `npm test`. Evidence: [frontend/package.json](frontend/package.json), [frontend/src/lib/financial-utils.test.ts](frontend/src/lib/financial-utils.test.ts). Runtime verification recorded 5/5 frontend tests passing. Evidence: [verification.md](verification.md).
- **Backend:** pytest, pytest-cov, and httpx are dependencies; route/helper tests are in `backend/tests/test_routes.py`. Evidence: [backend/requirements.txt](backend/requirements.txt), [backend/tests/test_routes.py](backend/tests/test_routes.py). Runtime verification recorded 15/15 backend tests passing. Evidence: [verification.md](verification.md).
- **Rule test:** The temporary frontend rule test ran with 6/6 tests passing and `git diff --check` passing; its assertion was then removed. Evidence: [rules-verification.md](rules-verification.md).

## Configuration

- **API origin override:** `VITE_API_BASE_URL` optionally prefixes frontend API requests; the committed example defines it as empty. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/.env.example](frontend/.env.example).
- **Development proxy:** Vite proxies `/api` to `http://backend:8000`. Evidence: [frontend/vite.config.ts](frontend/vite.config.ts). Runtime verification confirmed `GET http://localhost:5173/api/metrics` returned HTTP 200. Evidence: [verification.md](verification.md).