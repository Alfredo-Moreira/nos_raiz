# Architecture overview

Nôs Raiz is a single web application backed by PostgreSQL, deployed as one docker-compose stack on a Synology NAS. This document uses the [C4 model](https://c4model.com/) at the context and container levels.

## System context

Who uses the system and what it talks to.

```mermaid
flowchart TB
    family["Family members<br/>(admin, editor, viewer)"]
    app["Nôs Raiz<br/>Family tree web app"]
    other["Other genealogy software<br/>(Ancestry, FamilySearch, Gramps…)"]
    backup["Synology Hyper Backup<br/>(off-device copy)"]

    family -- "Browse and edit the tree<br/>HTTPS" --> app
    app -- "GEDCOM export file" --> other
    app -- "Nightly database dumps" --> backup
```

## Containers

What runs on the NAS.

```mermaid
flowchart LR
    browser["Browser<br/>React single-page app"]

    subgraph nas["Synology NAS · Container Manager (docker-compose)"]
        api["App container<br/>Node.js + Fastify<br/>serves API and static UI"]
        db[("PostgreSQL container<br/>pg_trgm, unaccent")]
        vol[["Shared-folder volumes<br/>database files, dumps,<br/>media (post-MVP)"]]
    end

    browser -- "JSON over HTTPS" --> api
    api -- "SQL via Drizzle ORM" --> db
    db --- vol
    api --- vol
```

| Container | Responsibility | Technology |
| --- | --- | --- |
| App | REST API, authentication, serving the built React UI, GEDCOM export, scheduled backup job | Node.js, Fastify, Drizzle ORM |
| PostgreSQL | Stores people, relationships, events, places, users and sessions | PostgreSQL with `pg_trgm` and `unaccent` extensions |

The UI is built at image build time and served as static files by the app container, so there is no separate web server container.

## Backend structure

```mermaid
flowchart TB
    routes["HTTP routes<br/>(Fastify plugins)"] --> services["Services<br/>validation, business rules"]
    services --> crud["Entity data access<br/>(Drizzle queries)"]
    services --> repo["GenealogyRepository<br/>ancestors, descendants,<br/>relationship path, common ancestors"]
    crud --> pg[(PostgreSQL)]
    repo --> pg
```

- **Routes** handle HTTP, authentication and authorisation checks.
- **Services** hold business rules, for example "a person cannot be their own ancestor" and "at most two biological parents".
- **Entity data access** is plain create, read, update and delete through Drizzle.
- **GenealogyRepository** is the only place that walks the family graph. Isolating it keeps a future move to a graph database to one module. See [ADR-0003](decisions/0003-postgresql-as-primary-database.md).

## Request flow example: "How are we related?"

```mermaid
sequenceDiagram
    participant Web as Browser
    participant Api as App (Fastify)
    participant Repo as GenealogyRepository
    participant Db as PostgreSQL

    Web->>Api: GET /api/relationship?from=p1&to=p2
    Api->>Api: Check session and viewer role
    Api->>Repo: findRelationshipPath(p1, p2)
    Repo->>Db: Recursive CTE: ancestors of p1 and of p2
    Db-->>Repo: Common ancestors with generation distances
    Repo-->>Api: Nearest common ancestor, path, degree
    Api-->>Web: "Second cousin" + path
```

## Related documents

- [Data model](data-model.md)
- [Tech stack](tech-stack.md)
- [Deployment](../operations/deployment.md)
- [Security overview](../security/overview.md)
