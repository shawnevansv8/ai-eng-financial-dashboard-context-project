# Current Status

## Runtime Status

- **Startup:** The documented command `docker compose up --build` was run successfully. Evidence: [README.md](README.md), [verification.md](verification.md).
- **Frontend:** The frontend container ran successfully and `http://localhost:5173/` returned HTTP 200. Evidence: [verification.md](verification.md).
- **Backend:** The backend container ran successfully on host port 8000, with debugpy published on port 5678. Evidence: [docker-compose.yml](docker-compose.yml), [verification.md](verification.md).
- **Health endpoint:** `GET http://localhost:8000/health` returned HTTP 200 with `{"status":"ok"}`. Evidence: [backend/app/routes.py](backend/app/routes.py), [verification.md](verification.md).
- **API:** `GET http://localhost:8000/api/metrics` returned HTTP 200 and JSON during runtime verification. Evidence: [backend/app/routes.py](backend/app/routes.py), [verification.md](verification.md).
- **Frontend proxy:** `GET http://localhost:5173/api/metrics` returned HTTP 200 and JSON during runtime verification. Evidence: [frontend/vite.config.ts](frontend/vite.config.ts), [verification.md](verification.md).

## Test Status

- **Frontend:** Runtime verification recorded `docker compose exec -T frontend npm test` passing 5/5 tests. Evidence: [verification.md](verification.md).
- **Backend:** Runtime verification recorded `docker compose exec -T backend env PYTHONDONTWRITEBYTECODE=1 pytest -p no:cacheprovider` passing 15/15 tests; the run emitted one FastAPI/TestClient deprecation warning. Evidence: [verification.md](verification.md).
- **Diff check:** The Phase 3 rule test recorded `git diff --check` passing. Evidence: [rules-verification.md](rules-verification.md).

## Repository Context

- [verification.md](verification.md) records the Phase 1 source/configuration investigation and runtime checks.
- [engineering-findings.md](engineering-findings.md) records Phase 2 conventions, risks, proposed rules, and unknowns grounded in repository evidence.
- [`.agents/rules/`](.agents/rules/) contains the active repository-specific constraints for API paths, financial contracts, logic boundaries, component structure, and deterministic mock data.
- [rules-verification.md](rules-verification.md) records the observed Phase 3 test showing the rules guided a minimal frontend change.
- [AGENTS.md](AGENTS.md) requires future agents to review `.agents/rules`, `.agents/skills`, and this `memory-bank` when available before acting.

## Known Limitations / Unknowns

- No authentication model is defined. Evidence: [verification.md](verification.md).
- No persistent database or storage architecture is defined; current movements are generated in memory. Evidence: [backend/app/routes.py](backend/app/routes.py), [verification.md](verification.md).
- No production deployment or production environment-variable policy is defined. Evidence: [engineering-findings.md](engineering-findings.md).
- No verified business users are defined. Evidence: [verification.md](verification.md).
- Compose defines no Docker health checks for either service, although the backend exposes `/health`. Evidence: [docker-compose.yml](docker-compose.yml), [backend/app/routes.py](backend/app/routes.py).
- No repository-wide Python formatting, linting, or static type-checking policy is configured by the inspected files. Evidence: [engineering-findings.md](engineering-findings.md).

## Future Agent Guidance

Read [AGENTS.md](AGENTS.md), all files in [`.agents/rules/`](.agents/rules/), and this memory bank before modifying the project. The rules define the verified constraints around frontend API paths, frontend/backend financial contracts, logic placement, component structure, and seeded mock-data behavior.

## Evidence Standard

Every major claim in this memory bank is grounded in the cited repository source, configuration, tests, or recorded runtime verification. Unknowns are identified rather than inferred.