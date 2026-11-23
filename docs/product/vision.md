# Product vision

## Mission

Connect generations across Cape Verde and its global diaspora by making it easy to build family trees, discover ancestral connections and preserve family histories.

## The problem

Cape Verdean family history is scattered. Relatives live on different islands and across the US, Portugal and Europe. Records are spread across parish books, civil registries and immigration files in several languages, and names are spelled differently in each (Gonçalves, Goncalves, Gonsalves). Much of what is known lives only in the memory of older relatives.

General-purpose genealogy services do not handle this well: they assume a single country's records and naming conventions, and they hold families' data on someone else's servers.

## Audience

| Phase | Who | What they need |
| --- | --- | --- |
| MVP | One family, self-hosted | A private place to record who is who, how they are related, and where they came from |
| Later | Many families in the diaspora | Their own trees, plus the ability to discover where their trees connect |

## Principles

1. **Family data belongs to the family.** Self-hostable, exportable (GEDCOM) at any time, never locked in.
2. **Model real families.** Remarriage, half-siblings, adoption, cousin marriages, uncertain dates and multiple name spellings are normal, not edge cases.
3. **Cape Verde first.** Places, naming conventions (including *nome de casa*) and languages (Portuguese, Kriolu, English) are first-class.
4. **Private by default.** Information about living people is visible only to signed-in family members.
5. **Simple to run.** One `docker compose up` on a home NAS; backups that a non-expert can restore.

## What success looks like

- **MVP:** The family records at least four generations together, and older relatives' knowledge is captured while it still can be.
- **Long term:** Diaspora families discover shared ancestors across trees they built independently.

See the [MVP scope](mvp.md) and [roadmap](roadmap.md).
