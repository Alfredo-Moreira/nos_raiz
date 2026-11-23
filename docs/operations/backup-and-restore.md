# Backup and restore

> **Status: open.** The backup strategy is not yet decided; the NAS owner will define it. Until then, **the app does not back up its own data**, and backing up the `nosraiz` database on the NAS PostgreSQL is the NAS owner's responsibility.

## What needs protecting

| Data | Where |
| --- | --- |
| The `nosraiz` database | Shared `postgres` container on the NAS (PostgreSQL 16) |
| Media (post-MVP) | To be decided |
| Configuration | `.env` (baked into the image; keep a copy somewhere safe) |

## Requirements for the strategy

Whatever is chosen should meet these, which come from the [threat model](../security/threat-model.md) (T8):

- **Logical dumps** (`pg_dump`) of the `nosraiz` database, not copies of the live data directory, which may be inconsistent mid-write.
- **At least one copy off the NAS**, so a disk failure, theft or fire does not lose everything.
- **Encrypted** wherever it leaves the NAS: the data contains full personal details.
- **Versioned**, so a bad edit discovered days later can still be undone.
- **A tested restore:** a backup that has never been restored is not a backup.

## Until then

A GEDCOM export from the app is a software-independent copy of the tree that an owner can save at any time.

The decision will be recorded as an ADR and this document rewritten.
