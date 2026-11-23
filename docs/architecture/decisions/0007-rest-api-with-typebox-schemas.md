# 0007. REST API with TypeBox schemas

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

The React UI needs an API, and the platform phase may add other clients (mobile apps, integrations). How should the API be designed and typed?

## Decision drivers

- Type safety between API and UI without duplicating definitions
- Request validation at the edge of the API
- Usable by non-TypeScript clients later
- Self-documenting

## Considered options

1. **REST with TypeBox schemas**
2. **tRPC**
3. **GraphQL**

## Decision outcome

Chosen option: **REST with JSON Schema definitions written in TypeBox**, kept in `packages/shared`.

- Fastify validates every request and serialises every response with the schemas.
- The UI imports the TypeScript types inferred from the same schemas.
- An OpenAPI document is generated from the schemas and served by the API.
- Resources are nested under a tree, because access is per tree ([ADR-0009](0009-per-tree-roles-and-protected-view.md)): for example `GET /api/trees/:treeId/people/:personId`.

### Consequences

- **Good:** One schema definition drives validation, types and documentation.
- **Good:** Any HTTP client can use the API.
- **Bad:** More boilerplate per endpoint than tRPC.
- **Bad:** Graph-shaped reads (a person with ancestors and descendants) need purpose-built endpoints rather than client-defined queries.

## Pros and cons of the options

### tRPC

- Good, because types flow from server to client with no schema step.
- Bad, because it is usable only from TypeScript clients.

### GraphQL

- Good, because clients can ask for exactly the graph shape they need.
- Bad, because it adds a schema language, resolver layer and caching complexity, and makes per-field access control (the protected view) harder to audit.
