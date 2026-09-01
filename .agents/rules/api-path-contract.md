# Rule

Preserve the frontend `/api` request path and its Vite proxy relationship with the Compose `backend` service. Use `VITE_API_BASE_URL` only as the optional API-origin prefix; do not hardcode a host URL or rename the `/api` path, `backend` service, or port 8000 without coordinated configuration and consumer changes.

## Evidence

- [frontend/src/App.tsx](frontend/src/App.tsx) builds its metrics request as `${API_BASE_URL}/api/metrics`, where `API_BASE_URL` is `VITE_API_BASE_URL` or an empty string.
- [frontend/vite.config.ts](frontend/vite.config.ts) proxies `/api` to `http://backend:8000`.
- [docker-compose.yml](docker-compose.yml) defines the `backend` service and publishes port 8000.
- [verification.md](verification.md) records that `GET http://localhost:5173/api/metrics` returned HTTP 200 through the live proxy.

## Why

The browser request path, Vite proxy target, and Compose service name are coupled. Changing one in isolation can prevent the frontend from reaching the backend in the verified local runtime.

## Example

When adding a metrics-summary request, use `${API_BASE_URL}/api/metrics/summary` rather than `http://localhost:8000/api/metrics/summary`.
