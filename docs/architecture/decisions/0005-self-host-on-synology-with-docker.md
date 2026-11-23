# 0005. Self-host on Synology with Docker

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

The MVP serves one family and holds private information about living relatives. The family already owns a Synology NAS. Where and how should the MVP run?

## Decision drivers

- Family data stays on hardware the family controls
- No recurring hosting cost
- Install and upgrade with minimal effort
- The same packaging should later work on a cloud host for the platform phase

## Considered options

1. **Docker containers on the Synology NAS** via Container Manager and docker-compose
2. **Native Synology package** (SPK)
3. **Cloud VPS** (any provider)

## Decision outcome

Chosen option: **Docker on the Synology NAS**, managed as a docker-compose project in Synology Container Manager.

- Two containers: the app (Node.js serving API and UI) and PostgreSQL.
- Data lives on volumes mapped to a NAS shared folder.
- The same `docker-compose.yml` runs on any Docker host, so a move to the cloud is a deployment change, not a code change.

Setup is described in [deployment](../../operations/deployment.md).

### Consequences

- **Good:** Data never leaves the family's hardware unless they choose an off-site backup.
- **Good:** No hosting bill; reproducible installs and upgrades (pull new image, restart).
- **Bad:** Availability depends on the home network and NAS; acceptable for a family app.
- **Bad:** Reaching the app from outside the home needs a remote-access decision (VPN, Tailscale or reverse proxy), still an [open question](../../README.md#open-questions).
- **Bad:** Container Manager is only available on Synology models that support it.
