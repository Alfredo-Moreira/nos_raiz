# 0003. Use PostgreSQL as the primary database

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

Family data is a graph (see [ADR-0002](0002-model-family-graph-as-edge-tables.md)), which suggests a graph database. But the MVP runs on a home Synology NAS with limited memory, holds thousands to tens of thousands of people, and also needs ordinary relational data: users, sessions, places, events. Which database should the MVP use, and how do we keep the option of a graph database open for the platform phase?

## Decision drivers

- Runs comfortably on a NAS with 2–8 GB of RAM shared with other services
- Ancestor, descendant and relationship queries fast at family scale
- Accent-insensitive, typo-tolerant name search (Gonçalves ≈ Goncalves ≈ Gonsalves)
- Simple, reliable backups that a non-expert can restore
- A path to the diaspora-wide platform without a rewrite

## Considered options

1. **PostgreSQL**
2. **Neo4j** (graph database)
3. **SQLite**
4. **PostgreSQL with the Apache AGE extension** (Cypher queries inside PostgreSQL)

## Decision outcome

Chosen option: **PostgreSQL**, with every graph traversal behind a `GenealogyRepository` interface, because it meets every driver at family scale and leaves a contained migration path.

Recursive CTEs answer ancestor, descendant and relationship queries in milliseconds at this scale. The `pg_trgm` and `unaccent` extensions provide the fuzzy name search. One database holds everything, at roughly 100 MB of RAM.

### Consequences

- **Good:** One database for graph data, users, sessions and metadata.
- **Good:** Low memory footprint; `pg_dump` backups; mature TypeScript tooling.
- **Good:** Strong integrity: foreign keys, unique and check constraints.
- **Bad:** Relationship-path queries are more verbose in SQL than in Cypher.
- **Bad:** Deep traversals slow down at millions of people with many hops. Not a concern for the MVP.

### Migration path to a graph database

Moving to a graph database later is a query rewrite, not a redesign.

| PostgreSQL | Property graph |
| --- | --- |
| `person` row | `(:Person)` node |
| `parent_child` row | `[:PARENT_OF {kind}]` relationship |
| `partnership` row | `[:PARTNER_OF {kind}]` relationship |
| `place` + `parent_place_id` | `(:Place)-[:IN]->(:Place)` |
| `event` row | `(:Event)` node or properties on its person |

1. **Data:** one ETL script exports the edge tables and loads them as nodes and relationships.
2. **Queries:** only the `GenealogyRepository` implementation changes from SQL to Cypher; callers are untouched.
3. **Everything else:** users, auth, sessions and media metadata stay in PostgreSQL.

Apache AGE is a middle step: Cypher queries over the same PostgreSQL database, with no second system to run.

### When to revisit

Revisit when any of these become true:

- Cross-tree matching across many families needs graph algorithms (similarity, community detection).
- Traversal queries exceed acceptable latency at platform scale.
- Hosting moves off the NAS, so the memory constraint no longer applies.

## Pros and cons of the options

### PostgreSQL

- Good, because it handles graph traversal (recursive CTEs) and relational data in one place.
- Good, because of fuzzy search extensions, low memory and easy backups.
- Bad, because path queries are wordier than in a graph query language.

### Neo4j

- Good, because path queries are short and natural, for example `shortestPath((a)-[:PARENT_OF*]-(b))`.
- Good, because built-in graph algorithms suit future cross-tree matching.
- Bad, because the JVM needs about 1–2 GB of RAM, a large share of a NAS.
- Bad, because Community Edition backups require stopping the database.
- Bad, because integrity constraints are weaker, and users and auth would need a second database.
- Bad, because TypeScript tooling is thinner than for SQL.

### SQLite

- Good, because it is a single file with trivial backups, and supports recursive CTEs.
- Bad, because fuzzy, accent-insensitive search is weaker.
- Bad, because it would need migrating for the multi-family platform.

### PostgreSQL with Apache AGE

- Good, because it offers Cypher queries without a second database.
- Bad, because it needs a custom PostgreSQL image and has a smaller community.
- Neutral: kept as the first step if graph queries are needed later.
