# 0002. Model the family graph as person nodes and edge tables

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

The core of Nôs Raiz is storing who is related to whom. Real families are not trees: every child has two parents, people have several partners, adoption and step-parenting are common, and in small island communities marriages between relatives make the same ancestor appear more than once (pedigree collapse). How should these relationships be stored?

## Decision drivers

- Must represent multiple partners, half-siblings, adoption and pedigree collapse without special cases
- Ancestor, descendant and relationship queries must be straightforward
- Must map cleanly to GEDCOM for export and future import
- Must map cleanly to a graph database if one is adopted later

## Considered options

1. **Parent pointers on person** (`mother_id`, `father_id` columns)
2. **GEDCOM-style family records** (a `family` row with two spouses; children attached to the family)
3. **Person nodes with typed edge tables** (`parent_child`, `partnership`)

## Decision outcome

Chosen option: **3, person nodes with typed edge tables**, because it handles every family shape uniformly and maps one-to-one to a property graph.

- `parent_child(parent_id, child_id, kind)` with kind biological, adoptive, step, foster or unknown
- `partnership(person_a_id, person_b_id, kind)` with kind marriage, civil, partner or unknown
- Siblings, cousins and other relationships are derived by queries, never stored

Details are in the [data model](../data-model.md).

### Consequences

- **Good:** Adoptive and biological parents can coexist; a single known parent is just one row.
- **Good:** Converting to a graph database is a direct mapping: rows become nodes and relationships.
- **Good:** Derived relationships can never be inconsistent with the stored edges.
- **Bad:** Rules such as "at most two biological parents" and "no cycles" must be enforced in the service layer rather than by the schema alone.
- **Bad:** GEDCOM export must synthesise `FAM` records by grouping children whose two parents are partners.

## Pros and cons of the options

### Parent pointers on person

- Good, because it is the simplest schema.
- Bad, because it assumes exactly one mother and one father, with no way to express adoption alongside biological parents.
- Bad, because it bakes in gendered roles.

### GEDCOM-style family records

- Good, because it matches GEDCOM directly.
- Bad, because single-parent and unknown-parent children need an artificial family with one spouse.
- Bad, because a child with different adoptive and biological families needs multiple family links with their own types, adding indirection to every query.

### Person nodes with typed edge tables

- Good, because every family shape is the same two kinds of row.
- Good, because it maps directly to a property graph.
- Bad, because some integrity rules live in application code.
