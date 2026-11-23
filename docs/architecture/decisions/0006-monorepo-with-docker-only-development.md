# 0006. Monorepo with pnpm and Docker-only development

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

The backend and frontend are both TypeScript ([ADR-0004](0004-typescript-fastify-drizzle-react.md)) and share API types and the fuzzy-date parser. Contributors may use any operating system, and the project should not depend on what is installed on a developer's machine. How should the code be organised, and how should it be run during development?

## Decision drivers

- Share types and logic between API and UI without publishing packages
- Any machine that can run Docker can develop, test and build the project
- Local checks and CI checks must be identical
- Tests must run against the same PostgreSQL major version as the deployment

## Considered options

1. **Monorepo, Docker-only development:** pnpm workspaces; every command runs inside containers
2. **Monorepo, tools installed on the host:** Node.js and pnpm on the developer's machine, PostgreSQL in Docker
3. **Separate repositories** for API and UI

## Decision outcome

Chosen option: **1, a monorepo with Docker-only development.**

### Repository layout

```
apps/
  api/        # Fastify REST API
  web/        # React + Vite UI
packages/
  shared/     # TypeBox schemas, API types, fuzzy-date parser
docker-compose.yml       # development stack
docker-compose.nas.yml   # MVP deployment (see ADR-0010)
Makefile                 # the only entry point for commands
```

### Package manager

**pnpm** workspaces, provided inside the containers by Node.js corepack. Nothing is installed on the host.

### Development stack

`docker-compose.yml` runs:

| Service | Purpose |
| --- | --- |
| `db` | PostgreSQL 16, the development database |
| `db-test` | PostgreSQL 16, recreated for each test run |
| `api` | The API with source mounted and hot reload |
| `web` | The Vite dev server with source mounted and hot reload |

`node_modules` lives in Docker volumes, never on the host, so host and container platforms cannot conflict.

### Commands

Every task is a `make` target that wraps `docker compose`, for example `make up`, `make down`, `make test`, `make lint`, `make typecheck`, `make migrate`. Host requirements are **Docker and make only**.

CI runs the same `make` targets, so "passes locally" and "passes in CI" mean the same thing.

Migrations are generated with drizzle-kit and committed. `make migrate` applies them in development; in deployment the app applies pending migrations on start, before accepting requests.

### Consequences

- **Good:** A new machine is ready after `git clone` and `make up`.
- **Good:** No "works on my machine" differences in Node.js, pnpm or PostgreSQL versions.
- **Good:** Shared types and the date parser are imported directly, with no publishing step.
- **Bad:** Editor integrations (type checking, ESLint) need `node_modules`; a VS Code Dev Container configuration can be added later to provide them.
- **Bad:** File watching through bind mounts can be slower on macOS than native.

## More information

Testcontainers was considered for database tests and rejected: it needs access to the Docker socket from inside a container. A dedicated `db-test` service achieves the same isolation.
