# Roadmap

The roadmap moves from one family's tree on a home NAS to a diaspora-wide platform on production infrastructure. Phases are ordered by dependency, not dated.

```mermaid
flowchart LR
    M["MVP<br/>One family, home NAS"] --> V["v1<br/>Richer records"] --> P["Platform<br/>Many families, connected"]
```

## Before coding

1. Nodes design session: settle what a person, event and place hold.
2. Choose the tree-rendering library and supporting tools.
3. Developer docs (`docs/development/`): getting started, conventions.

## MVP: one family, home NAS

Detailed in [MVP scope](mvp.md).

- People, relationships, events, places, fuzzy dates
- Tree view, person pages, relationship finder, fuzzy name search
- Google sign-in (invite-only), per-tree roles, protected view by default, share links
- GEDCOM export
- Deployed on the home NAS through the Cloudflare tunnel

**Gate to v1:** MVP exit criteria met; the family has used it for real entry; backup strategy in place.

## v1: richer records

- GEDCOM import (5.5.1 and 7.0)
- Photos and documents attached to people and events, protected by default
- Sources and citations (parish register, civil record, oral history)
- Portuguese and Kriolu user interface
- Printable charts and family group sheets
- Change history: who changed what, with undo

**Gate to platform:** data model proven stable in real use; production hosting decided.

## Platform: many families, connected

- Production infrastructure (new ADR; see the triggers in [ADR-0010](../architecture/decisions/0010-mvp-on-home-nas.md#when-to-move-off-the-nas))
- Open sign-up; multiple independent trees (the data model already supports them)
- More sign-in providers (Apple, Microsoft, Facebook…)
- Cross-tree matching ("this Gonçalves from Brava, born c. 1872, may be the same person in another family's tree")
- Shared place gazetteer for Cape Verde islands and diaspora communities
- Re-evaluate the database for graph workloads at this scale; see [ADR-0003](../architecture/decisions/0003-postgresql-as-primary-database.md)
