# Tech stack

TypeScript end to end, PostgreSQL for storage, Docker on a Synology NAS for hosting. Each choice below links to the decision record that explains it.

| Layer | Choice | Why | Alternatives considered | Decision |
| --- | --- | --- | --- | --- |
| Language | TypeScript | One language for API and UI; shared types for API contracts | Python, Go | [ADR-0004](decisions/0004-typescript-fastify-drizzle-react.md) |
| Runtime | Node.js (current LTS) | Mature, runs well in a small container | Deno, Bun | [ADR-0004](decisions/0004-typescript-fastify-drizzle-react.md) |
| Web framework | Fastify | Lightweight, fast, schema-based validation, good TypeScript support | NestJS, Express | [ADR-0004](decisions/0004-typescript-fastify-drizzle-react.md) |
| ORM and migrations | Drizzle ORM + drizzle-kit | SQL-first, so recursive CTEs stay readable; typed schema; versioned migrations | Prisma, Kysely | [ADR-0004](decisions/0004-typescript-fastify-drizzle-react.md) |
| Database | PostgreSQL with `pg_trgm`, `unaccent` | Recursive queries, fuzzy accent-insensitive search, low memory, scales to the platform | Neo4j, SQLite, Apache AGE | [ADR-0003](decisions/0003-postgresql-as-primary-database.md) |
| Frontend | React + Vite | Largest ecosystem for tree and graph visualisation | Svelte, Vue | [ADR-0004](decisions/0004-typescript-fastify-drizzle-react.md) |
| Tree rendering | To be prototyped: family-chart, or custom D3 + ELK layout | Must handle multiple partners and pedigree collapse | — | Open question |
| Interchange | GEDCOM 5.5.1 export (widest support across genealogy software); 7.0 export later | Data is never locked in | — | [MVP scope](../product/mvp.md) |
| Packaging | Docker, docker-compose | One-command install on the NAS | Native Synology package | [ADR-0005](decisions/0005-self-host-on-synology-with-docker.md) |
| Hosting | Synology Container Manager | The family's existing hardware | Cloud VPS | [ADR-0005](decisions/0005-self-host-on-synology-with-docker.md) |
| Backups | `pg_dump` nightly + Synology Hyper Backup | Built into the NAS; copies leave the device | — | [Backup and restore](../operations/backup-and-restore.md) |

## Resource budget

Synology NAS models commonly have 2–8 GB of RAM shared with other services. The stack should stay under roughly 512 MB at idle:

| Component | Expected idle memory |
| --- | --- |
| PostgreSQL | ~100 MB |
| Node.js app | ~100–150 MB |

This budget is one reason a JVM-based graph database was rejected for the MVP.

## Testing (planned)

| Level | Tool |
| --- | --- |
| Unit | Vitest |
| Database and repository | Vitest against a real PostgreSQL container (Testcontainers) |
| End to end | Playwright |

The repository layer is tested against a fixed sample family that includes remarriage, half-siblings, adoption and a cousin marriage.
