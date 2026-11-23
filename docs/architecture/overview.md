# Architecture overview

Nôs Raiz is a single web application backed by PostgreSQL. For the MVP it runs on the home NAS, reachable through a Cloudflare tunnel ([ADR-0010](decisions/0010-mvp-on-home-nas.md)). This document uses the [C4 model](https://c4model.com/) at the context and container levels.

## System context

Who uses the system and what it talks to.

```mermaid
flowchart TB
    members["Tree members<br/>(owner, editor, viewer)"]
    visitors["Link visitors<br/>(no account)"]
    app["Nôs Raiz<br/>Family tree web app"]
    google["Google<br/>(OAuth sign-in)"]
    other["Other genealogy software<br/>(Ancestry, FamilySearch, Gramps…)"]

    members -- "Browse and edit<br/>HTTPS" --> app
    visitors -- "View via share link<br/>HTTPS" --> app
    app -- "Verify sign-in" --> google
    app -- "GEDCOM export file" --> other
```

## Containers

What runs, for the MVP on the home NAS.

```mermaid
flowchart LR
    browser["Browser<br/>React single-page app"]
    cf["Cloudflare edge"]

    subgraph nas["Home NAS · UGOS Docker"]
        tunnel["cloudflared"]
        zx["Zoraxy"]
        api["nos-raiz container<br/>Node.js + Fastify<br/>serves API and static UI"]
        db[("Shared postgres container<br/>PostgreSQL 16<br/>nosraiz database")]
    end

    browser -- "JSON over HTTPS" --> cf
    cf --> tunnel --> zx
    zx -- "internal_public_apps" --> api
    api -- "SQL via Drizzle<br/>nas-bridge" --> db
```

| Container | Responsibility | Technology |
| --- | --- | --- |
| nos-raiz | REST API, Google sign-in, sessions, visibility filter, serving the built React UI, GEDCOM export | Node.js, Fastify, Drizzle ORM |
| postgres (shared) | Stores trees, members, people, relationships, events, places, sessions | PostgreSQL 16 with `pg_trgm` and `unaccent`, dedicated `nosraiz` database |

The UI is built at image build time and served as static files by the app container, so there is no separate web server.

## Backend structure

```mermaid
flowchart TB
    routes["HTTP routes<br/>(Fastify plugins, TypeBox schemas)"] --> auth["Auth hook<br/>who is asking, which tree, which role"]
    auth --> services["Services<br/>business rules"]
    services --> crud["Entity data access<br/>(Drizzle queries)"]
    services --> repo["GenealogyRepository<br/>ancestors, descendants,<br/>relationship path, common ancestors"]
    services --> vis["Visibility filter<br/>full or protected shape"]
    crud --> pg[(PostgreSQL)]
    repo --> pg
    vis --> routes
```

- **Routes** handle HTTP. Requests and responses are validated against TypeBox schemas from `packages/shared` ([ADR-0007](decisions/0007-rest-api-with-typebox-schemas.md)).
- **Auth hook** resolves the caller: a signed-in member with a role in this tree, a share-link visitor, or nobody. Routes declare the minimum role they need.
- **Services** hold business rules, for example "no cycles", "at most two biological parents", "a tree always has an owner", "living people cannot be made public".
- **Entity data access** is plain create, read, update and delete through Drizzle.
- **GenealogyRepository** is the only place that walks the family graph ([ADR-0003](decisions/0003-postgresql-as-primary-database.md)).
- **Visibility filter** shapes every person and event for the caller's role before it leaves the server ([ADR-0009](decisions/0009-per-tree-roles-and-protected-view.md)).

## Request flow example: a link visitor asks "How are we related?"

```mermaid
sequenceDiagram
    participant Web as Browser
    participant Api as App (Fastify)
    participant Repo as GenealogyRepository
    participant Vis as Visibility filter
    participant Db as PostgreSQL

    Web->>Api: GET /api/trees/t1/relationship?from=p1&to=p2 (share-link token)
    Api->>Db: Look up token hash, check not revoked or expired
    Api->>Api: Caller = link visitor on tree t1
    Api->>Repo: findRelationshipPath(p1, p2)
    Repo->>Db: Recursive CTE over parent_child
    Db-->>Repo: Common ancestors with generation distances
    Repo-->>Api: Path and degree
    Api->>Vis: Shape people on the path for "link visitor"
    Vis-->>Api: Names, years, countries only
    Api-->>Web: "Second cousin" + protected path
```

## Related documents

- [Data model](data-model.md)
- [Tech stack](tech-stack.md)
- [Deployment](../operations/deployment.md)
- [Security overview](../security/overview.md)
