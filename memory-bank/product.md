# Product

## Purpose

This repository implements a financial metrics dashboard. The current UI presents financial movement-derived metrics and charts. Evidence: [README.md](README.md), [frontend/src/App.tsx](frontend/src/App.tsx).

## Current Capabilities

- **Financial metrics:** The dashboard displays total income, total outcome, profit, and profit margin from loaded financial movements. Evidence: [frontend/src/components/dashboard/kpi-row.tsx](frontend/src/components/dashboard/kpi-row.tsx), [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts).
- **Income and outcome information:** Financial movements contain dates, amounts, operation type, category, and B2B/B2C business type. Evidence: [backend/app/routes.py](backend/app/routes.py), [frontend/src/lib/financial-types.ts](frontend/src/lib/financial-types.ts).
- **Charts:** The UI renders monthly income-versus-outcome and profit-margin line charts. Evidence: [frontend/src/components/dashboard/income-outcome-chart.tsx](frontend/src/components/dashboard/income-outcome-chart.tsx), [frontend/src/components/dashboard/profit-percent-chart.tsx](frontend/src/components/dashboard/profit-percent-chart.tsx).
- **Filtering:** The metrics endpoints support date, category, operation-type, and selected business-type filters where defined. Evidence: [backend/app/routes.py](backend/app/routes.py), [backend/tests/test_routes.py](backend/tests/test_routes.py).
- **Facets and summaries:** The API provides metrics facets and period summaries with day, week, and month grouping. Evidence: [backend/app/routes.py](backend/app/routes.py), [backend/tests/test_routes.py](backend/tests/test_routes.py).
- **Comparisons and alerts:** The API provides current-versus-previous-period net comparisons and outcome-increase alert candidates. Evidence: [backend/app/routes.py](backend/app/routes.py), [backend/tests/test_routes.py](backend/tests/test_routes.py).
- **Top categories and business-type endpoints:** The API provides top categories and B2B/B2C-specific metrics endpoints. Evidence: [backend/app/routes.py](backend/app/routes.py), [backend/tests/test_routes.py](backend/tests/test_routes.py).

## Data Flow

1. The backend generates seeded financial movements and exposes them through `GET /api/metrics`. Evidence: [backend/app/routes.py](backend/app/routes.py).
2. The frontend calls `${API_BASE_URL}/api/metrics` with native `fetch`; without an override, Vite proxies `/api` to `backend:8000`. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/vite.config.ts](frontend/vite.config.ts).
3. The frontend converts the returned movements into KPI and monthly chart data in `frontend/src/lib/financial-utils.ts`. Evidence: [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts).
4. `App.tsx` passes the resulting data and loading state to dashboard components for presentation. Evidence: [frontend/src/App.tsx](frontend/src/App.tsx), [frontend/src/components/dashboard/kpi-row.tsx](frontend/src/components/dashboard/kpi-row.tsx).

## Known Unknowns

- Authentication is not established by repository source or configuration. Evidence: [verification.md](verification.md).
- Persistent storage or database architecture is not established; the current backend generates movements in memory. Evidence: [backend/app/routes.py](backend/app/routes.py), [verification.md](verification.md).
- Production deployment is not established by repository source or configuration. Evidence: [verification.md](verification.md), [engineering-findings.md](engineering-findings.md).
- Verified business users are not established by repository source or documentation. Evidence: [verification.md](verification.md).