# Nôs Raiz

Nôs Raiz ("our roots" in Cape Verdean Kriolu) is a genealogy platform for building family trees, discovering ancestral connections, and preserving family histories, with a mission to connect generations across Cape Verde and its global diaspora.

## Status

**Pre-MVP: design phase.** There is no application code yet. The architecture, scope and key decisions are documented in [`docs/`](docs/README.md).

The MVP is a private, self-hosted family tree app running on a Synology NAS for a single family.

## At a glance

| | |
| --- | --- |
| Backend | TypeScript, Node.js, Fastify, Drizzle ORM |
| Frontend | React |
| Database | PostgreSQL |
| Hosting | Docker (docker-compose) on Synology Container Manager |
| Interchange | GEDCOM export (import later) |

## Documentation

- [Product vision](docs/product/vision.md)
- [MVP scope](docs/product/mvp.md)
- [Roadmap](docs/product/roadmap.md)
- [Architecture overview](docs/architecture/overview.md)
- [Data model](docs/architecture/data-model.md)
- [Tech stack](docs/architecture/tech-stack.md)
- [Architecture decision records](docs/architecture/decisions/README.md)
- [Security overview](docs/security/overview.md) and [threat model](docs/security/threat-model.md)
- [Deployment](docs/operations/deployment.md) and [backup and restore](docs/operations/backup-and-restore.md)
- [Glossary](docs/glossary.md)

## Getting started

Setup instructions will be added once the first application code lands. Deployment is planned as a single `docker compose up` on the NAS; see [deployment](docs/operations/deployment.md).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Security issues: see [SECURITY.md](SECURITY.md).

## License

Not yet decided. See [open questions](docs/README.md#open-questions).
