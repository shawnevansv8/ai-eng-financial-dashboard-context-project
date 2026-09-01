# Rule

Place dashboard-specific components in `frontend/src/components/dashboard` and reusable visual primitives in `frontend/src/components/ui`. Use kebab-case filenames, PascalCase component and prop-interface names, and typed props; keep data loading and state in the appropriate parent rather than embedding it in presentational cards or charts.

## Evidence

- [frontend/src/components/dashboard](frontend/src/components/dashboard) contains dashboard-specific files such as `kpi-card.tsx` and `income-outcome-chart.tsx`.
- [frontend/src/components/ui](frontend/src/components/ui) contains reusable primitives such as `card.tsx` and `skeleton.tsx`.
- [frontend/src/components/dashboard/kpi-card.tsx](frontend/src/components/dashboard/kpi-card.tsx) demonstrates a kebab-case file, PascalCase `KPICard` and `KPICardProps`, and typed props.
- [frontend/src/App.tsx](frontend/src/App.tsx) owns the API request, local state, and loading/error behavior before passing values to dashboard components.

## Why

This keeps UI ownership clear, makes reusable primitives discoverable, and preserves the current typed data-flow pattern.

## Example

Implement a top-categories visualization as `frontend/src/components/dashboard/top-category-chart.tsx` with typed data props supplied by its parent, rather than placing the request inside a generic UI card.
