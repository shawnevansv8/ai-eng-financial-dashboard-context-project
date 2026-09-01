# Engineering Findings

## Existing Conventions

- **Frontend organization:** Dashboard-specific components are in [frontend/src/components/dashboard](frontend/src/components/dashboard), reusable primitives are in [frontend/src/components/ui](frontend/src/components/ui), and types and transformations are in [frontend/src/lib](frontend/src/lib). [frontend/src/App.tsx](frontend/src/App.tsx) composes the page and loads data.
- **Frontend naming:** Component files use kebab-case, such as [frontend/src/components/dashboard/kpi-card.tsx](frontend/src/components/dashboard/kpi-card.tsx), while exported components and prop interfaces use PascalCase, such as `KPICard` and `KPICardProps`.
- **Backend organization:** FastAPI application setup is in [backend/app/main.py](backend/app/main.py). API models, mock-data generation, helper functions, and route handlers are currently organized together in [backend/app/routes.py](backend/app/routes.py).
- **API requests:** [frontend/src/App.tsx](frontend/src/App.tsx) uses native `fetch`, validates `response.ok`, and calls `${API_BASE_URL}/api/metrics`. The current source does not demonstrate a shared API client.
- **TypeScript types:** Domain unions and interfaces, including `FinancialMovement`, are centralized in [frontend/src/lib/financial-types.ts](frontend/src/lib/financial-types.ts). Components declare typed props, and type-only imports are used in [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts) and dashboard components.
- **Python typing/models:** [backend/app/routes.py](backend/app/routes.py) uses `Literal` aliases, typed parameters and return values, PEP 604 optional types, Pydantic `BaseModel` schemas, and FastAPI `response_model` declarations.
- **Data transformation:** Frontend KPI, monthly aggregation, and formatting functions are pure utilities in [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts). Backend filtering, ordering, summaries, category aggregation, comparisons, alerts, and mock generation reside in [backend/app/routes.py](backend/app/routes.py).
- **Error handling:** [frontend/src/App.tsx](frontend/src/App.tsx) throws for non-OK API responses, shows an error state, and ends loading in `finally`. Dashboard charts render explicit loading and no-data states. Evidence: [frontend/src/components/dashboard/income-outcome-chart.tsx](frontend/src/components/dashboard/income-outcome-chart.tsx), [frontend/src/components/dashboard/profit-percent-chart.tsx](frontend/src/components/dashboard/profit-percent-chart.tsx).
- **Environment configuration:** `VITE_API_BASE_URL` is an optional prefix and is empty in [frontend/.env.example](frontend/.env.example). Vite proxies `/api` to `http://backend:8000`. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/vite.config.ts](frontend/vite.config.ts).
- **Testing:** Frontend utility tests are colocated in [frontend/src/lib/financial-utils.test.ts](frontend/src/lib/financial-utils.test.ts). Backend route and helper tests are in [backend/tests/test_routes.py](backend/tests/test_routes.py).
- **Linting/build:** Frontend scripts provide linting, building, development, preview, tests, watch mode, and coverage. ESLint is configured for TypeScript, React Hooks, and React Refresh. Evidence: [frontend/package.json](frontend/package.json), [frontend/eslint.config.js](frontend/eslint.config.js). The TypeScript app config enables strict unused-code checks and no emit. Evidence: [frontend/tsconfig.app.json](frontend/tsconfig.app.json).
- **Docker:** Compose defines `frontend` and `backend`, with frontend depending on backend. The services publish 5173, 8000, and backend debug port 5678. Evidence: [docker-compose.yml](docker-compose.yml), [frontend/Dockerfile](frontend/Dockerfile), [backend/Dockerfile](backend/Dockerfile).
- **Imports:** Frontend application imports use the `@/` alias configured in [frontend/vite.config.ts](frontend/vite.config.ts) and [frontend/tsconfig.app.json](frontend/tsconfig.app.json); tests use relative imports. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/src/lib/financial-utils.test.ts](frontend/src/lib/financial-utils.test.ts).
- **Existing agent instructions:** [AGENTS.md](AGENTS.md) requires agents to review `.agents/rules`, `.agents/skills`, and optional `memory-bank` before acting. These directories are not presently in the repository.

## Risks for Future AI Edits

- **Frontend/backend API contract:** The frontend expects `FinancialMovement` JSON fields in snake_case, including `create_date`, `operation_type`, and `business_type`, and calculates displayed metrics from these fields. Altering the backend schema without coordinated frontend and test updates can break the dashboard. Evidence: [frontend/src/lib/financial-types.ts](frontend/src/lib/financial-types.ts), [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts), [backend/app/routes.py](backend/app/routes.py).
- **Vite proxy and Compose wiring:** The Vite proxy target uses the Compose service name `backend` on port 8000. Renaming that service, changing its port, or changing the `/api` path without coordinated changes can break browser requests. Evidence: [frontend/vite.config.ts](frontend/vite.config.ts), [docker-compose.yml](docker-compose.yml). Runtime verification confirmed `http://localhost:5173/api/metrics` returns HTTP 200 through this proxy.
- **Seeded mock data:** Routes call `generate_mock_movements(seed=42)`, and tests assert count, sort order, filters, and response behavior. Modifying generation or filtering semantics can change API behavior and regress tests. Evidence: [backend/app/routes.py](backend/app/routes.py), [backend/tests/test_routes.py](backend/tests/test_routes.py).
- **Wrong-layer financial calculations:** Frontend display calculations are independently implemented and tested as library functions, while backend API operations are separately implemented and tested. Moving or duplicating calculations without an explicit requirement can create inconsistent financial results. Evidence: [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts), [frontend/src/lib/financial-utils.test.ts](frontend/src/lib/financial-utils.test.ts), [backend/app/routes.py](backend/app/routes.py).
- **Component structure:** `App.tsx` owns loading and state; dashboard components receive typed data and loading props; shared visual primitives remain under `components/ui`. Placing request or unrelated domain behavior inside cards or charts would diverge from the existing organization. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/src/components/dashboard/kpi-card.tsx](frontend/src/components/dashboard/kpi-card.tsx), [frontend/src/components/ui/card.tsx](frontend/src/components/ui/card.tsx).
- **AGENTS.md instructions:** Acting without first checking the instruction, skill, and memory locations specified in [AGENTS.md](AGENTS.md) conflicts with the established agent workflow.

## Proposed Repository Rules

### 1. Preserve the `/api` frontend-backend path contract

**Rule:** Use `VITE_API_BASE_URL` as the optional external prefix and retain the Vite `/api` proxy relationship with the Compose `backend` service unless the change explicitly includes coordinated configuration updates.

**Repository evidence:** [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/vite.config.ts](frontend/vite.config.ts), [docker-compose.yml](docker-compose.yml).

**Reason:** This preserves the verified browser-to-backend request path across the Compose environment.

**Example:** A request for metric summaries should use `${API_BASE_URL}/api/metrics/summary`, rather than hardcoding `http://localhost:8000`.

### 2. Keep financial API schemas aligned with the `FinancialMovement` contract

**Rule:** When changing financial movement fields or semantics, update the backend Pydantic model, frontend TypeScript type, consumers, and relevant tests together.

**Repository evidence:** [backend/app/routes.py](backend/app/routes.py), [frontend/src/lib/financial-types.ts](frontend/src/lib/financial-types.ts), [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts).

**Reason:** The frontend directly consumes the backend JSON and relies on exact field names and typed values.

**Example:** Adding a movement currency field requires changes to the backend response schema, `FinancialMovement`, and calculations or UI tests that use it.

### 3. Keep dashboard presentation calculations in frontend lib and API operations in backend helpers

**Rule:** Put typed, reusable dashboard presentation calculations in `frontend/src/lib`; keep filtering, aggregation, comparisons, alerts, ordering, and mock generation in backend helpers.

**Repository evidence:** [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts), [backend/app/routes.py](backend/app/routes.py), [frontend/src/lib/financial-utils.test.ts](frontend/src/lib/financial-utils.test.ts).

**Reason:** This maintains the existing separation between client display transformation and server data operations.

**Example:** A new display formatter belongs in `financial-utils.ts`; a new filtered API aggregate belongs in a backend helper and route.

### 4. Follow existing dashboard/ui component boundaries and naming conventions

**Rule:** Place dashboard-specific components in `components/dashboard`, reusable primitives in `components/ui`, use kebab-case filenames, PascalCase component and prop-interface names, and typed props.

**Repository evidence:** [frontend/src/components/dashboard](frontend/src/components/dashboard), [frontend/src/components/ui](frontend/src/components/ui), [frontend/src/components/dashboard/kpi-card.tsx](frontend/src/components/dashboard/kpi-card.tsx).

**Reason:** This preserves the codebase's current UI ownership boundaries and discoverable naming pattern.

**Example:** A new top-categories chart should be implemented as `components/dashboard/top-category-chart.tsx`, with data supplied by an appropriate parent.

### 5. Preserve seeded mock-data behavior and focused test coverage

**Rule:** Preserve the deterministic seeded mock-data behavior and update focused tests whenever generator, filtering, sorting, validation, or response behavior changes.

**Repository evidence:** [backend/app/routes.py](backend/app/routes.py), [backend/tests/test_routes.py](backend/tests/test_routes.py).

**Reason:** API endpoints consistently generate from `seed=42`, and tests validate expected data properties and endpoint behavior.

**Example:** Changing `/api/metrics/categories/top` limit behavior requires retaining or intentionally revising the limit validation and sorted-result test coverage.

## Unverified Items

- A repository-wide Python formatting, linting, or static type-checking policy is not configured by the inspected files.
- A shared frontend API client pattern is not established; only one direct native `fetch` request exists. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx).
- A production deployment process is not defined by the inspected repository files.
- Persistent database or storage configuration is not defined; the backend currently generates mock movements in memory. Evidence: [backend/app/routes.py](backend/app/routes.py).
- An authentication model is not defined by the inspected repository files.
- A production environment-variable policy is not defined; only the optional frontend `VITE_API_BASE_URL` development configuration is evidenced. Evidence: [frontend/.env.example](frontend/.env.example).
- Docker health checks are not configured for either service. The backend exposes `/health`, but Compose declares no healthcheck. Evidence: [backend/app/routes.py](backend/app/routes.py), [docker-compose.yml](docker-compose.yml).

## Phase 2 Evidence Standard

These proposed rules were derived from actual repository source, configuration, tests, and prior runtime verification. They are not generic best-practice recommendations and do not rely on assumptions about behavior or architecture not demonstrated by the repository.