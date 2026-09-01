# Rules Verification

## Test Task

"Add a reusable frontend utility that formats a numeric profit-margin value as a percentage, and add a focused test for it."

## Agent Behavior

The agent inspected the repository rules and the existing frontend utility and test patterns. It found that `formatPercent()` already existed in `frontend/src/lib/financial-utils.ts` and already provided the requested reusable percentage formatting.

Instead of duplicating the utility, the agent added focused negative-margin coverage to the existing colocated test file, `frontend/src/lib/financial-utils.test.ts`.

## Rules That Influenced the Result

- **financial-logic-boundaries.md:** This rule directed the agent to retain reusable dashboard presentation logic in `frontend/src/lib`. It led to reusing `formatPercent()` in `financial-utils.ts` rather than introducing a duplicate formatter in another layer or location. Evidence: [.agents/rules/financial-logic-boundaries.md](.agents/rules/financial-logic-boundaries.md).
- **component-structure.md:** This rule directed the agent to preserve the current component and ownership boundaries. It led to placing the focused coverage in the existing utility test instead of creating a component, a new UI abstraction, or unrelated architecture. Evidence: [.agents/rules/component-structure.md](.agents/rules/component-structure.md).
- **financial-data-contract.md:** Reviewed but not applicable because the task did not change backend responses, API schemas, or the `FinancialMovement` contract. Evidence: [.agents/rules/financial-data-contract.md](.agents/rules/financial-data-contract.md).
- **api-path-contract.md:** Reviewed but not applicable because the task did not add or alter frontend API requests, Vite proxy settings, Compose service names, or ports. Evidence: [.agents/rules/api-path-contract.md](.agents/rules/api-path-contract.md).
- **deterministic-mock-data.md:** Reviewed but not applicable because the task did not change backend mock-data generation, the seed, or backend tests. Evidence: [.agents/rules/deterministic-mock-data.md](.agents/rules/deterministic-mock-data.md).

## Validation

- Frontend tests: `docker compose exec -T frontend npm test` passed with 6/6 tests.
- `git diff --check` passed.
- No commit was made during the test.

## Result

The rules successfully steered the agent based on observed behavior: it discovered and reused the existing formatter, avoided duplicate implementation and unrelated architecture, and added focused coverage in the established test location.

## Test Diff

The temporary test added a negative profit-margin assertion: `formatPercent(-12.345)` expected `"-12.3%"`. The assertion was removed after validation because it was created solely for the rule test.