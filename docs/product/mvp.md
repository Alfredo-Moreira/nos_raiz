# MVP scope

The MVP is a family tree app running on the home NAS as a **family showcase and functional test** ([ADR-0010](../architecture/decisions/0010-mvp-on-home-nas.md)). One family, one tree, invite-only Google sign-in. The family starts from scratch, so fast manual entry matters more than import.

## In scope

### People and relationships

- Create, edit and delete people: given names, surnames, nickname (*nome de casa*), sex, living/deceased, notes.
- Record alternate names (birth, married, spelling variants).
- Link parents and children, with the kind of link: biological, adoptive, step, foster, unknown.
- Link partners: marriage, civil union, partner, unknown.
- Add a relative directly from a person ("add mother", "add child", "add partner").

The exact fields on a person are settled in the nodes design session; see the [data model](../architecture/data-model.md).

### Events and places

- Record events: birth, baptism, marriage, divorce, death, burial, emigration, residence.
- Enter fuzzy dates ("c. 1890", "before 1902", "1880–1885") and see them sorted sensibly.
- Pick or create places in a hierarchy (village → island → country).

### Viewing and finding

- Interactive tree view centred on any person: ancestors and descendants.
- Person page: details, events, parents, partners, children, siblings (derived).
- "How are we related?" between any two people.
- Name search that ignores accents and tolerates spelling variants.

### Sign-in, roles and sharing

See [ADR-0008](../architecture/decisions/0008-oauth-only-sign-in.md) and [ADR-0009](../architecture/decisions/0009-per-tree-roles-and-protected-view.md).

- Sign in with Google only. Invite-only: owners invite family members by email.
- Roles per tree: **owner** (several allowed), **editor**, **viewer**.
- **Protected view by default:** viewers and link visitors see names, relationships, years and countries only, never full dates, towns or notes.
- Owners can mark individual **deceased** people as public (full details for all).
- Owners create and revoke **share links**: read-only, whole tree, protected view, no sign-in needed.
- Each member can be linked to their own person in the tree.

### Data portability

- Export the tree as GEDCOM (owners and editors).

## Out of scope for the MVP

| Feature | Why later | Planned for |
| --- | --- | --- |
| Backups built into the app | NAS owner's strategy, to be decided | Open |
| GEDCOM import | The family starts from scratch | v1 |
| Photos, documents, media | Storage and privacy design needed | v1 |
| Sources and citations | Needed for research quality, not for first entry | v1 |
| Portuguese and Kriolu UI | English first, but strings externalised from day one | v1 |
| Sign-in with other social providers | Google covers the family | Platform |
| Multiple trees, open sign-up | Platform phase; the data model already supports multiple trees | Platform |
| Cross-tree matching | Needs many trees | Platform |
| Mobile apps | Responsive web is enough | Not planned |

## User stories and acceptance criteria

| # | As a… | I want to… | Done when |
| --- | --- | --- | --- |
| 1 | editor | add my grandmother and her parents in a few minutes | Adding a relative from a person page takes one form and links automatically |
| 2 | editor | record that my great-grandfather was born "about 1890" in Brava | The date is stored as entered, sorts before 1891, and the place nests under Cabo Verde |
| 3 | editor | record that my grandfather had children with two partners | The tree shows both partnerships and the half-siblings correctly |
| 4 | viewer | see how I am related to a cousin I just met | Selecting two people returns the relationship ("second cousin") and the path |
| 5 | viewer | find "Goncalves" when it was entered as "Gonçalves" | Search is accent-insensitive and tolerant of small spelling differences |
| 6 | owner | invite my cousin as a viewer | They sign in with Google, see the tree in the protected view, and cannot edit |
| 7 | owner | send a link to a relative without a Google account | They open the link and see the whole tree in the protected view, without signing in |
| 8 | owner | stop a link from working | After revoking it, the link shows "no longer available" |
| 9 | link visitor | look up my grandmother | I see her name, birth year and country, but not her full birth date or village |
| 10 | owner | share the full baptism record of a great-great-grandfather | After marking him public, viewers and link visitors see his full details; a living person cannot be marked public |
| 11 | owner | get all our data out | GEDCOM export opens in another genealogy program |

## MVP exit criteria

- All eleven stories pass.
- A sample family with remarriage, half-siblings, adoption and a cousin marriage displays and queries correctly.
- Automated tests prove no protected field reaches a viewer or link visitor on any endpoint.
- Running on the NAS, reachable through the tunnel, with the family signed in.
