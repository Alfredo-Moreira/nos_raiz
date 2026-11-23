# 0004. Use TypeScript with Fastify, Drizzle and React

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

> **Note (2026-11-22):** The package manager is pnpm, and all tooling runs inside Docker; see [ADR-0006](0006-monorepo-with-docker-only-development.md).

## Context and problem statement

The project is built and maintained by a small team, initially one person. Which language and frameworks should the backend and frontend use?

## Decision drivers

- The maintainer is most comfortable with TypeScript
- One language across backend and frontend reduces context switching and allows shared types
- Recursive SQL must stay readable, not hidden behind an ORM abstraction
- Small memory footprint on the NAS
- A strong ecosystem for interactive tree and graph visualisation

## Considered options

1. **TypeScript:** Node.js + Fastify + Drizzle ORM, React frontend
2. **Python:** FastAPI + SQLAlchemy/Alembic, React frontend
3. **Go:** standard library or a light router, sqlc, React frontend

## Decision outcome

Chosen option: **TypeScript with Fastify, Drizzle ORM and React (Vite)**, because it matches the maintainer's skills and gives one language end to end.

- **Fastify** over NestJS: lighter, less framework ceremony for a small app; schema-based request validation.
- **Drizzle** over Prisma: SQL-first, so recursive CTEs are written as readable SQL with typed results; lightweight runtime; versioned migrations with drizzle-kit.
- **React + Vite:** the largest ecosystem for tree and graph rendering libraries.

### Consequences

- **Good:** API request and response types are shared between server and client.
- **Good:** A single toolchain (npm, Vitest, ESLint) for the whole repository.
- **Bad:** Node.js uses more memory than a Go binary, though it stays within the NAS budget.
- **Bad:** Python's data tooling would help future record-matching work; that can be a separate service if needed.

## More information

The tree-rendering library is still an [open question](../../README.md#open-questions), to be settled by a prototype.
