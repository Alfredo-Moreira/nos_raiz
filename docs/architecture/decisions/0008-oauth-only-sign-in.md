# 0008. OAuth-only sign-in, Google first

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

The app is reachable from the internet ([ADR-0010](0010-mvp-on-home-nas.md)) and holds personal family data. Users need to sign in. Should the app manage passwords, use external identity providers, or both?

## Decision drivers

- No password storage: nothing to leak, reset or brute-force
- Family members already have Google accounts
- The platform phase will support other social providers
- Phase 1 is invite-only

## Considered options

1. **OAuth only**, Google first, other providers later
2. **Username and password**
3. **Both**

## Decision outcome

Chosen option: **OAuth only. Google in phase 1; other social providers (such as Apple, Microsoft, Facebook) when the platform opens.** The app never stores or accepts passwords.

- `app_user` stores the provider identity (`provider`, `provider_account_id`) and the verified email, never a password hash.
- **Invite-only in phase 1:** a Google sign-in succeeds only if its verified email has been invited to at least one tree.
- **Bootstrap:** when the app starts with no trees, it creates one named from the `TREE_NAME` setting and an invitation making `OWNER_EMAIL` its owner. The first sign-in with that Google account claims it.
- Sessions are server-side, stored in PostgreSQL, referenced by an `HttpOnly`, `Secure`, `SameSite=Lax` cookie.
- People without an account can still view a tree through a share link ([ADR-0009](0009-per-tree-roles-and-protected-view.md)).
- The authentication library is chosen when sign-in is built, and recorded in its own ADR.

### Consequences

- **Good:** No password hashing, reset flows or credential-stuffing exposure.
- **Good:** Google handles multi-factor authentication and account recovery.
- **Bad:** A family member without a Google account cannot sign in until other providers are added. They can still use a share link.
- **Bad:** Google sign-in needs a public HTTPS callback URL, so even the MVP must be reachable on a real domain.
