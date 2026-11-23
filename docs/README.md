# Nôs Raiz documentation

This folder is the source of truth for what Nôs Raiz is, how it is built, and why.

## Map

| Area | Document | Answers |
| --- | --- | --- |
| Product | [Vision](product/vision.md) | Why does this exist and who is it for? |
| | [MVP](product/mvp.md) | What is in the first release, and what is not? |
| | [Roadmap](product/roadmap.md) | What comes after the MVP? |
| Architecture | [Overview](architecture/overview.md) | What are the parts and how do they talk? |
| | [Data model](architecture/data-model.md) | How is a family stored and queried? |
| | [Tech stack](architecture/tech-stack.md) | Which technologies, and why each one? |
| | [Decisions (ADRs)](architecture/decisions/README.md) | Why was each significant choice made? |
| Security | [Overview](security/overview.md) | How are accounts, access and private data handled? |
| | [Threat model](security/threat-model.md) | What could go wrong and what mitigates it? |
| Operations | [Deployment](operations/deployment.md) | How is the MVP deployed on the home NAS? |
| | [Backup and restore](operations/backup-and-restore.md) | How will family data be protected against loss? (open) |
| Reference | [Glossary](glossary.md) | What do genealogy and Cape Verdean terms mean? |

## Conventions

- Docs are Markdown; diagrams use [Mermaid](https://mermaid.js.org/) so they render on GitHub and live next to the text.
- Architecture follows a lightweight [C4 model](https://c4model.com/) (context and container levels).
- Decisions are recorded as ADRs in [MADR](https://adr.github.io/madr/) format. See [CONTRIBUTING.md](../CONTRIBUTING.md#architecture-decisions).
- Developer setup docs (`docs/development/`) will be added with the first application code.

## Open questions

These are tracked here until decided, then recorded in the relevant doc or ADR.

| Question | Next step | Affects |
| --- | --- | --- |
| What a person, event and place hold | Dedicated nodes design session | [Data model](architecture/data-model.md) |
| Tree-rendering library | Evaluate libraries and skills after the nodes session | [Tech stack](architecture/tech-stack.md) |
| Authentication library | Choose when sign-in is built | [ADR-0008](architecture/decisions/0008-oauth-only-sign-in.md) |
| Backup strategy | NAS owner to decide | [Backup and restore](operations/backup-and-restore.md), [threat model](security/threat-model.md) T8 |
| Production hosting | Decide before opening beyond the family | [ADR-0010](architecture/decisions/0010-mvp-on-home-nas.md#when-to-move-off-the-nas) |
