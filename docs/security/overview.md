# Security overview

Nôs Raiz holds personal information about living family members: names, birth dates, birthplaces and relationships. These are exactly the details used for identity theft and security-question answers, so the app is **private by default**: nothing is visible without signing in.

The risks this design responds to are in the [threat model](threat-model.md).

## Authentication

- Username and password accounts, created by an admin (no public sign-up).
- Passwords hashed with **Argon2id**. Plaintext passwords are never stored or logged.
- Minimum password length of 12 characters; checked against a list of common passwords.
- Server-side sessions stored in PostgreSQL, referenced by a cookie that is `HttpOnly`, `Secure` and `SameSite=Lax`. Because the cookie is `Secure`, the app is always served over HTTPS, even on the home network (see [deployment](../operations/deployment.md#first-install)).
- Sessions expire after 30 days of inactivity and can be revoked by an admin.
- Login attempts are rate-limited per account and per IP address.

## Authorisation

| Role | Read tree | Edit tree | Export GEDCOM | Manage users |
| --- | --- | --- | --- | --- |
| viewer | Yes | No | No | No |
| editor | Yes | Yes | Yes | No |
| admin | Yes | Yes | Yes | Yes |

- Every API route declares the minimum role it needs; the check runs before the handler.
- The UI hides controls a user cannot use, but the server is the only enforcement point.

## Living-person privacy

- A person is treated as **living** unless they have a death event or were born more than 110 years ago.
- In the MVP all signed-in users are family, so living people are visible to them.
- GEDCOM export marks living people. A "privatise living people" export option replaces their details with "Living" for sharing outside the family.
- The platform phase will add per-tree visibility; living people are never public.

## Application security

- All input validated against JSON schemas at the route (Fastify).
- All database access through parameterised queries (Drizzle); no string-built SQL.
- React escapes output by default; no use of `dangerouslySetInnerHTML` with user content.
- Security headers: `Content-Security-Policy`, `X-Content-Type-Options`, `Referrer-Policy`, `Strict-Transport-Security` when served over HTTPS.
- CSRF protection on state-changing requests (SameSite cookies plus a CSRF token).
- Dependencies checked with `npm audit` and Dependabot.

## Secrets

- Database password, session secret and similar values come from environment variables in a `.env` file on the NAS, never committed to the repository.
- `.env.example` in the repository documents the variables without values.
- The PostgreSQL container is not exposed outside the Docker network.

## Data protection

- Nightly backups; see [backup and restore](../operations/backup-and-restore.md).
- Off-device backup copies must be encrypted (Hyper Backup client-side encryption enabled).
- No real family data in the repository, tests or screenshots. See [CONTRIBUTING.md](../../CONTRIBUTING.md#privacy).

## Reporting a vulnerability

See [SECURITY.md](../../SECURITY.md).
