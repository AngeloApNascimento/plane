# Agent Development Guide

## Commands

- `pnpm dev` - Start all dev servers (web:3000, admin:3001)
- `pnpm build` - Build all packages and apps
- `pnpm check` - Run all checks (format, lint, types)
- `pnpm check:lint` - OxLint across all packages
- `pnpm check:types` - TypeScript type checking
- `pnpm fix` - Auto-fix format and lint issues
- `pnpm turbo run <command> --filter=<package>` - Target specific package/app

## Code Style

- **Imports**: Use `workspace:*` for internal packages, `catalog:` for external deps
- **TypeScript**: Strict mode enabled, all files must be typed
- **Formatting**: oxfmt, run `pnpm fix:format`
- **Linting**: OxLint with shared `.oxlintrc.json` config
- **Naming**: camelCase for variables/functions, PascalCase for components/types
- **Error Handling**: Use try-catch with proper error types, log errors appropriately
- **State Management**: MobX stores in `packages/shared-state`, reactive patterns
- **Testing**: All features require unit tests, use existing test framework per package
- **Components**: Primitives come from the published `@makeplane/propel` npm package (`@makeplane/propel/components/*`, `elements/*`, `icons`); composite/Plane-specific components live in `@plane/blocks` (`packages/blocks`, subpath imports only, e.g. `@plane/blocks/toast`)

## Backend tests (Docker)

The Django/pytest suite for `apps/api` runs in an isolated stack defined by `docker-compose-test.yml` at the repo root.

Prereq (once): `./setup.sh` — generates `apps/api/.env` from `.env.example`.

- Full suite: `docker compose -f docker-compose-test.yml up --build --abort-on-container-exit --exit-code-from api-tests`
- Subset: `docker compose -f docker-compose-test.yml run --rm api-tests pytest -m unit`
- Teardown: `docker compose -f docker-compose-test.yml down -v`

See `apps/api/tests/RUNNING_TESTS.md` for the full walkthrough and troubleshooting; see `apps/api/tests/TESTING_GUIDE.md` for test conventions and fixtures.

## Base44 Docker setup

The Base44 dev environment is defined by `docker-compose.base44.yml` at the repo root.

- Start: `docker compose -f docker-compose.base44.yml up -d --build`
- Stop: `docker compose -f docker-compose.base44.yml down`
- Logs: `docker compose -f docker-compose.base44.yml logs -f <service>`
- Preview: the web app is served on host port 3000.

### Architecture (single-origin)

All frontend apps (web, admin, space, live) run in a single `web` container via `pnpm dev` (turbo). The web Vite dev server (port 3000) proxies `/api`, `/auth`, `/static` to the Django API and `/uploads` to MinIO, so the browser sees a single origin — no CORS or cookie SameSite issues. Admin (3001), space (3002), and live (3100) are exposed on their own host ports.

### Key env files

- `apps/api/.env` — Django API config (DB, Redis, RabbitMQ, MinIO). `SECRET_KEY` and `LIVE_SERVER_SECRET_KEY` are placeholders overridden by `/run/base44/app.env`.
- `apps/web/.env`, `apps/admin/.env`, `apps/space/.env` — Vite env vars. `VITE_API_BASE_URL` is empty (relative) so API calls go through the Vite proxy.
- `apps/live/.env` — Live collaboration server config.

### Vite config changes for Docker

All three frontend Vite configs (`apps/web`, `apps/admin`, `apps/space`) bind to `host: true` with `allowedHosts: true` and include a `server.proxy` block forwarding API/auth/static/uploads paths to the `api` and `plane-minio` compose services.

### Secrets

`SECRET_KEY` (Django) and `LIVE_SERVER_SECRET_KEY` (live server auth) are generated as development placeholders via the Base44 platform and delivered to `/run/base44/app.env`. Both are `requiredAtBoot`. Replace them with real values for production.

### Verifying the app

1. `docker compose -f docker-compose.base44.yml ps` — all services should be healthy.
2. `curl -s http://localhost:3000/` — should return the Plane HTML shell.
3. `curl -s http://localhost:3000/api/instances/` — should return instance config JSON.
4. Open the preview at port 3000 — should show the Plane welcome/sign-in page.
