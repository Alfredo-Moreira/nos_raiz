# Nôs Raiz

Nôs Raiz ("our roots" in Cape Verdean Kriolu) is a genealogy platform for building family trees, discovering ancestral connections, and preserving family histories, with a mission to connect generations across Cape Verde and its global diaspora.

## Status

**Pre-MVP: design phase.** There is no application code yet. The architecture, scope and key decisions are documented in [`docs/`](docs/README.md).

The MVP is a family tree app for one family, running on a home NAS as a showcase and functional test, with invite-only Google sign-in and shareable read-only links.

## At a glance

| | |
| --- | --- |
| Backend | TypeScript, Node.js, Fastify, Drizzle ORM, REST |
| Frontend | React, Vite |
| Database | PostgreSQL 16 |
| Sign-in | Google (OAuth only) |
| Development | Docker Compose only, driven by `make` |
| MVP hosting | Home NAS (UGOS, arm64) behind a Cloudflare tunnel |

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

Developer setup will be documented in `docs/development/` with the first application code. The only host requirements will be Docker and make.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Security issues: see [SECURITY.md](SECURITY.md).

## License

Copyright © 2026 Alfredo Ramos Moreira. All rights reserved. See [LICENSE](LICENSE).
