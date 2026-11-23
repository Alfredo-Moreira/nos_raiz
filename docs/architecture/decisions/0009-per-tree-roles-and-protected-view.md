# 0009. Per-tree roles and a protected view by default

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

A family tree is full of the details used for identity theft: full names, exact birth dates, birthplaces and mothers' maiden names. Yet the point of the app is to share the tree with family, including relatives who will only ever open a link. Who sees what?

## Decision drivers

- The default must never expose enough to steal an identity
- The default must still show enough to recognise who someone is
- Owners need full details to do genealogy
- Access must be per tree, because users will belong to several trees on the platform
- Enforcement must be impossible to bypass from the browser

## Decision outcome

### Trees are the unit of access

- Every person belongs to exactly one `tree`.
- `tree_member(tree_id, user_id, role)` gives a user a role in a tree. A user can be a member of many trees with a different role in each.
- The MVP has one tree; the model is ready for many.

### Roles

| Role | Sees | Can edit | Manages members and share links |
| --- | --- | --- | --- |
| **Owner** | Full details | Yes | Yes |
| **Editor** | Full details | Yes | No |
| **Viewer** (signed-in member) | Protected view | No | No |
| **Link visitor** (no account) | Protected view | No | No |

- A tree may have several owners, and must always have at least one: the last owner cannot leave or be demoted.
- Any owner can add, remove or change the role of any member, including other owners.

### The protected view is the default for every person

Viewers and link visitors see, for every person:

| Shown | Never shown |
| --- | --- |
| Given names, surnames, nickname (*nome de casa*) | Full dates (day and month) |
| Sex; living or deceased | Places below country level (village, town, island) |
| Relationships (the whole tree shape) | Notes |
| The year of every event | Photos, documents and sources (post-MVP) |
| Country of each event | |

Name plus year plus country is enough to recognise "Maria, born 1931 in Cabo Verde". It is not enough to answer "date of birth" or "place of birth" on a form.

**Accepted trade-off:** because names and relationships are visible, a mother's birth surname is visible. Hiding it would mean hiding names, which defeats the purpose of the tree. The other identifying pieces (full date, birthplace) are withheld.

### Owners can open up individual deceased ancestors

An owner can mark an individual **deceased** person as `full_details_public`. Everyone who can see the tree then sees that person in full. This serves genealogy research on long-dead ancestors. Living people can never be marked public.

"Deceased" here means the person has a recorded **death or burial event**, not merely `is_living = false`. Otherwise an editor could flip `is_living` and an owner could then publish a living person. Removing the last death or burial event clears `full_details_public`.

### Share links

- Created and revoked by owners only.
- A share link is a long random token; it can optionally expire.
- Anyone with the link sees the **whole tree** in the protected view, read-only, without signing in.

### Enforcement lives in the API

The API builds every response through one **visibility filter** that takes the viewer's role and returns either the full or the protected shape. Protected fields are never sent to the browser. Automated tests assert, per role and per endpoint, exactly which fields are returned.

### Consequences

- **Good:** A forwarded share link cannot leak identity-theft details.
- **Good:** Per-tree roles need no migration when the platform adds multiple trees.
- **Good:** One filter is one place to audit.
- **Bad:** Every read endpoint must go through the visibility filter; a forgotten endpoint is a leak. The per-role tests are the safeguard.
- **Bad:** Viewers who are trusted relatives cannot see full dates unless they are made editors. A read-only "trusted viewer" role can be added later if needed.
