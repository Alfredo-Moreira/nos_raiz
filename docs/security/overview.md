# Security overview

Nôs Raiz holds personal information about family members: names, dates, places and relationships, which are exactly the details used for identity theft. The app is publicly reachable ([ADR-0010](../architecture/decisions/0010-mvp-on-home-nas.md)), so its design rule is: **by default, the tree shows enough to recognise who someone is, but never enough to steal their identity.** Full details are reserved for the people a tree's owners trust.

The risks this design responds to are in the [threat model](threat-model.md).

## Authentication

Decided in [ADR-0008](../architecture/decisions/0008-oauth-only-sign-in.md).

- **OAuth only.** Google sign-in in phase 1; other social providers when the platform opens. The app never accepts or stores passwords.
- **Invite-only in phase 1.** A Google sign-in is accepted only if its verified email has been invited to a tree. The first owner's email comes from the `OWNER_EMAIL` setting.
- **Sessions** are stored server-side in PostgreSQL, referenced by a cookie that is `HttpOnly`, `Secure` and `SameSite=Lax`. They expire after 30 days of inactivity and can be revoked.
- Sign-in attempts and the API are rate-limited per client IP. Behind the tunnel every request arrives from Zoraxy, so the client IP is taken from Cloudflare's `CF-Connecting-IP` header, trusted only when the request comes from the Zoraxy network.

## Authorisation

Decided in [ADR-0009](../architecture/decisions/0009-per-tree-roles-and-protected-view.md). Every role belongs to a specific tree; a user can be a member of several trees with a different role in each.

| Role | Sees | Edit people and events | Manage members | Manage share links |
| --- | --- | --- | --- | --- |
| Owner | Full details | Yes | Yes | Yes |
| Editor | Full details | Yes | No | No |
| Viewer (signed-in member) | Protected view | No | No | No |
| Link visitor (no account) | Protected view | No | No | No |

- A tree can have several owners and always has at least one.
- Every API route declares the minimum role it needs, checked before the handler runs.
- The UI hides controls a user cannot use, but the server is the only enforcement point.

## What each role sees

The **protected view** applies to every person in the tree, living or dead.

| Field | Full details (owner, editor) | Protected view (viewer, link visitor) |
| --- | --- | --- |
| Given names, surnames, nickname | Yes | Yes |
| Sex; living or deceased | Yes | Yes |
| Relationships | Yes | Yes |
| Event dates | Full date as entered | Year only |
| Event places | Full place (village, island, country) | Country only |
| Notes | Yes | No |
| Photos, documents, sources (post-MVP) | Yes | No |

**Exception:** an owner can mark an individual **deceased** person as public. That person is then shown with full details to everyone who can see the tree. Living people can never be made public.

**Accepted trade-off:** names and relationships are visible, so a mother's birth surname can be read from the tree. Full birth dates and birthplaces, the other pieces an identity thief needs, are withheld.

### Enforcement

- One **visibility filter** in the API shapes every person and event before it leaves the server. Protected fields are never sent to the browser, not merely hidden by the UI.
- Automated tests assert, per role and per endpoint, exactly which fields are returned, and fail if any protected field appears.

## Share links

- Created and revoked by owners only.
- A long random token (at least 128 bits of randomness); only its hash is stored.
- Optional expiry date.
- Grants read-only access to the whole tree in the protected view, without signing in.
- Pages viewed through a share link send `Referrer-Policy: no-referrer` and `X-Robots-Tag: noindex`, so the token does not leak to other sites or search engines.

## Application security

- All input validated against JSON schemas at the route (Fastify with TypeBox).
- All database access through parameterised queries (Drizzle); no string-built SQL.
- React escapes output by default; no `dangerouslySetInnerHTML` with user content.
- Security headers: `Content-Security-Policy`, `X-Content-Type-Options`, `Referrer-Policy`, `Strict-Transport-Security`.
- CSRF protection on state-changing requests (SameSite cookies plus a CSRF token).
- Dependencies checked with `pnpm audit` in CI and Dependabot.

## Infrastructure

- The app is reachable only through the Cloudflare tunnel and Zoraxy; the container publishes no ports.
- The container runs read-only, as a non-root user, with all Linux capabilities dropped.
- The app's PostgreSQL role owns only the `nosraiz` database and has no other privileges; no other role may connect to that database.

## Secrets

- The database URL, Google OAuth client secret and session secret come from a `.env` file that is never committed. `.env.example` documents the variables without values.
- The `.env` is baked into the image at build time (the home-lab convention), so the image must only ever be pushed to the private LAN registry, never a public one.

## Data protection

- No real family data in the repository, tests or screenshots. See [CONTRIBUTING.md](../../CONTRIBUTING.md#privacy).
- Backups are not yet designed; see [backup and restore](../operations/backup-and-restore.md).

## Reporting a vulnerability

See [SECURITY.md](../../SECURITY.md).
