# Rule

Keep financial API schemas aligned with the `FinancialMovement` contract. When a financial movement field or its meaning changes, update the backend Pydantic model and API response, the frontend TypeScript type and consumers, and the affected tests together.

## Evidence

- [backend/app/routes.py](backend/app/routes.py) defines the Pydantic `FinancialMovement` response model with `create_date`, `amount`, `operation_type`, `category`, and `business_type`.
- [frontend/src/lib/financial-types.ts](frontend/src/lib/financial-types.ts) defines the matching TypeScript `FinancialMovement` interface using those snake_case fields.
- [frontend/src/lib/financial-utils.ts](frontend/src/lib/financial-utils.ts) consumes `operation_type`, `amount`, and `create_date` to calculate dashboard values.
- [backend/tests/test_routes.py](backend/tests/test_routes.py) asserts filter and record behavior for these API fields.

## Why

The frontend consumes backend JSON directly. Uncoordinated field-name, type, or semantic changes can break financial calculations and rendered data.

## Example

Adding a currency field to movements requires a backend model and response update, a `FinancialMovement` interface update, and updates to any calculation or rendering tests that consume the field.
