# MVP scope

The MVP is a private family tree app that one family runs on their own Synology NAS. The family starts from scratch, so fast manual entry matters more than import.

## In scope

### People and relationships

- Create, edit and delete people: given names, surnames, nickname (*nome de casa*), sex, living/deceased, notes.
- Record alternate names (birth, married, spelling variants).
- Link parents and children, with the kind of link: biological, adoptive, step, foster, unknown.
- Link partners: marriage, civil union, partner, unknown.
- Add a relative directly from a person ("add mother", "add child", "add partner").

### Events and places

- Record events: birth, baptism, marriage, divorce, death, burial, emigration, residence.
- Enter fuzzy dates ("c. 1890", "before 1902", "1880–1885") and see them sorted sensibly.
- Pick or create places in a hierarchy (village → island → country).

### Viewing and finding

- Interactive tree view centred on any person: ancestors and descendants.
- Person page: details, events, parents, partners, children, siblings (derived).
- "How are we related?" between any two people.
- Name search that ignores accents and tolerates spelling variants.

### Accounts

- Sign in with username and password.
- Roles: **admin** (manage users), **editor** (change the tree), **viewer** (read only).
- Each user can be linked to their own person in the tree.

### Data safety

- Export the whole tree as GEDCOM.
- Automated nightly database backup.

## Out of scope for the MVP

| Feature | Why later | Planned for |
| --- | --- | --- |
| GEDCOM import | The family starts from scratch | v1 |
| Photos, documents, media | Storage and privacy design needed | v1 |
| Sources and citations | Needed for research quality, not for first entry | v1 |
| Portuguese and Kriolu UI | English first, but strings externalised from day one | v1 |
| Multiple trees / multiple families | Platform phase | Platform |
| Public profiles, sharing outside the family | Privacy design needed | Platform |
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
| 6 | admin | create an account for a family member as a viewer | They can sign in and browse but cannot edit |
| 7 | admin | get all our data out | GEDCOM export opens in another genealogy program |
| 8 | admin | recover after the NAS fails | A documented restore from backup brings the tree back |

## MVP exit criteria

- All eight stories pass.
- A sample family with remarriage, half-siblings, adoption and a cousin marriage displays and queries correctly.
- A restore from backup has been performed successfully at least once.
