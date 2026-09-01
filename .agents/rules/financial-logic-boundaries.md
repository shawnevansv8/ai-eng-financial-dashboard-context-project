# Rule

Keep reusable dashboard presentation calculations in `frontend/src/lib` and API data operations in backend helpers. Do not move or duplicate filtering, aggregation, ordering, comparison, alert, or mock-data logic across layers unless the repository architecture is intentionally changed with supporting evidence.

## Evidence

- [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts) contains typed KPI, monthly-data, currency, and percentage presentation functions.
- [frontend/src/lib/financial-utils.test.ts](frontend/src/lib/financial-utils.test.ts) tests the frontend calculation and formatting behavior directly.
- [backend/app/routes.py](backend/app/routes.py) contains mock generation, filtering, ordering, summaries, category aggregation, comparisons, and alert detection used by API routes.
- [backend/tests/test_routes.py](backend/tests/test_routes.py) tests backend route and helper behavior.

## Why

The repository separates frontend display transformations from backend data operations. Duplicating or relocating financial calculations can produce inconsistent results and bypass the existing focused tests.

## Example

Add a new display formatter to `frontend/src/lib/financial-utils.ts`; add a new date-filtered aggregate as a backend helper and expose it through a route.
