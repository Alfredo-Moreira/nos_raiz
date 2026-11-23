# 0001. Record architecture decisions

- **Status:** Accepted
- **Date:** 2026-11-22
- **Deciders:** Project owner

## Context and problem statement

Nôs Raiz will grow from a family MVP into a larger platform, possibly with more contributors. Decisions made now (database, data model, hosting) will be questioned later. Without a record of why they were made, they get re-argued or reversed without understanding the trade-offs.

## Decision outcome

Record every significant architectural decision as an Architecture Decision Record in `docs/architecture/decisions/`, using the [MADR](https://adr.github.io/madr/) format and the project [template](template.md).

- Files are numbered sequentially: `NNNN-short-title.md`.
- Accepted ADRs are immutable in substance. A changed decision gets a new ADR that supersedes the old one.
- The [index](README.md) lists every ADR with its status.

### Consequences

- **Good:** New contributors can learn why the system looks the way it does.
- **Good:** Revisiting a decision starts from the original reasoning and the conditions it named for revisiting.
- **Bad:** A small amount of writing overhead per decision.
