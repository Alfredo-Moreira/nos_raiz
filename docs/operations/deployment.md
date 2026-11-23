# Deployment

Nôs Raiz runs as a docker-compose project in Synology **Container Manager**: one app container and one PostgreSQL container, with data on a NAS shared folder. See [ADR-0005](../architecture/decisions/0005-self-host-on-synology-with-docker.md).

> **Status:** planned. The files referenced here (`docker-compose.yml`, `.env.example`, the app image) will be added with the first application code. This document describes the intended setup.

## Prerequisites

- A Synology NAS that supports Container Manager (DSM 7.2 or later)
- At least 1 GB of RAM free for the stack
- An admin account on the NAS

## Layout on the NAS

```
/volume1/docker/nos-raiz/
├── docker-compose.yml
├── .env                 # secrets, never committed
├── postgres/            # PostgreSQL data directory (volume)
├── backups/             # nightly pg_dump files (volume)
└── media/               # uploaded photos and documents (post-MVP)
```

## Configuration

Settings come from `.env`, copied from `.env.example` in the repository.

| Variable | Purpose |
| --- | --- |
| `POSTGRES_USER` | Database user, e.g. `nosraiz` |
| `POSTGRES_DB` | Database name, e.g. `nosraiz` |
| `POSTGRES_PASSWORD` | Database password, long and random |
| `DATABASE_URL` | Connection string the app uses: `postgres://<POSTGRES_USER>:<POSTGRES_PASSWORD>@db:5432/<POSTGRES_DB>` |
| `SESSION_SECRET` | Signs session cookies, long and random |
| `APP_PORT` | Port the app listens on, on the NAS |
| `BACKUP_RETENTION_DAYS` | How many nightly dumps to keep (default 30) |

## Planned compose shape

```yaml
services:
  app:
    image: nos-raiz/app:<version>
    env_file: .env
    ports:
      - "${APP_PORT}:3000"
    volumes:
      - ./backups:/backups
      - ./media:/media
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:<pinned version>
    env_file: .env
    volumes:
      - ./postgres:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB}"]
    restart: unless-stopped
    # No published ports: reachable only from the app container
```

## First install

1. Create the `/volume1/docker/nos-raiz/` folder in File Station.
2. Copy `docker-compose.yml` and `.env.example` into it; rename `.env.example` to `.env` and fill in values.
3. In Container Manager, go to **Project → Create**, choose the folder, and start the project.
4. The app runs database migrations on start-up.
5. Create the first admin account. On first start, with no users in the database, the app prints a one-time setup link to its logs (Container Manager → Container → app → Log). Opening it lets you choose the admin's username and password, and the link then expires.
6. Put HTTPS in front of the app, even on the home network (session cookies are `Secure` and will not work over plain HTTP). In DSM, go to **Control Panel → Login Portal → Advanced → Reverse Proxy** and map `https://<hostname>:443` to `http://localhost:<APP_PORT>` using the NAS's certificate.
7. Open `https://<hostname>` from a device on the home network.

## Upgrades

1. Read the [changelog](../../CHANGELOG.md) for the new version.
2. Make sure last night's backup exists (or run one manually).
3. Change the app image version in `docker-compose.yml`.
4. In Container Manager, rebuild or restart the project. Migrations run automatically.

## Remote access

Access from outside the home network is an [open question](../README.md#open-questions). Options and their risks are compared in the [threat model](../security/threat-model.md#remote-access-options-for-t2). Until it is decided, the app is reachable only on the home network.
