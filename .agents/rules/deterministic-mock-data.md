# Rule

Preserve the seeded mock-data behavior used by metrics routes. Do not casually change the `seed=42` calls or mock-movement generation, filtering, sorting, validation, or response behavior; update focused backend tests whenever an intentional behavior change is made.

## Evidence

- [backend/app/routes.py](backend/app/routes.py) defines `generate_mock_movements(seed)` and every metrics route invokes it with `seed=42`.
- [backend/tests/test_routes.py](backend/tests/test_routes.py) verifies generated movement count and chronological ordering, date/category/operation filters, B2B/B2C behavior, facets, summaries, top categories, comparisons, and alerts.
- [verification.md](verification.md) records that the backend suite passed 15/15 tests during runtime verification.

## Why

Metrics routes derive their responses from generated in-memory data. Changing deterministic generation or the covered operations changes API behavior and can invalidate test expectations.

## Example

When changing the `/api/metrics/categories/top` limit behavior, retain or deliberately revise its bounds and sorted-result tests in `backend/tests/test_routes.py`.