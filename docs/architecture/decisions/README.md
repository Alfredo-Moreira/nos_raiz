# Architecture decision records

Each significant technical decision is recorded as an ADR in [MADR](https://adr.github.io/madr/) format. The process for proposing one is in [CONTRIBUTING.md](../../../CONTRIBUTING.md#architecture-decisions).

| ADR | Title | Status | Date |
| --- | --- | --- | --- |
| [0001](0001-record-architecture-decisions.md) | Record architecture decisions | Accepted | 2026-11-22 |
| [0002](0002-model-family-graph-as-edge-tables.md) | Model the family graph as person nodes and edge tables | Accepted | 2026-11-22 |
| [0003](0003-postgresql-as-primary-database.md) | Use PostgreSQL as the primary database | Accepted | 2026-11-22 |
| [0004](0004-typescript-fastify-drizzle-react.md) | Use TypeScript with Fastify, Drizzle and React | Accepted | 2026-11-22 |
| [0005](0005-self-host-on-synology-with-docker.md) | Self-host on Synology with Docker | Superseded by 0010 | 2026-11-22 |
| [0006](0006-monorepo-with-docker-only-development.md) | Monorepo with pnpm and Docker-only development | Accepted | 2026-11-22 |
| [0007](0007-rest-api-with-typebox-schemas.md) | REST API with TypeBox schemas | Accepted | 2026-11-22 |
| [0008](0008-oauth-only-sign-in.md) | OAuth-only sign-in, Google first | Accepted | 2026-11-22 |
| [0009](0009-per-tree-roles-and-protected-view.md) | Per-tree roles and a protected view by default | Accepted | 2026-11-22 |
| [0010](0010-mvp-on-home-nas.md) | MVP runs on the home NAS as a showcase and test environment | Accepted | 2026-11-22 |

New ADRs start from the [template](template.md).

## Planned

| Topic | When |
| --- | --- |
| Authentication library | When sign-in is built |
| Tree-rendering library | After the nodes design session |
| Backup strategy | Owner to decide |
| Production hosting | Before opening beyond the family |
