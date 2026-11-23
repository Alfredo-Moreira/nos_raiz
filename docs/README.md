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
| Operations | [Deployment](operations/deployment.md) | How is it installed on the Synology NAS? |
| | [Backup and restore](operations/backup-and-restore.md) | How is family data protected against loss? |
| Reference | [Glossary](glossary.md) | What do genealogy and Cape Verdean terms mean? |

## Conventions

- Docs are Markdown; diagrams use [Mermaid](https://mermaid.js.org/) so they render on GitHub and live next to the text.
- Architecture follows a lightweight [C4 model](https://c4model.com/) (context and container levels).
- Decisions are recorded as ADRs in [MADR](https://adr.github.io/madr/) format. See [CONTRIBUTING.md](../CONTRIBUTING.md#architecture-decisions).
- Developer setup docs (`docs/development/`) will be added with the first application code.

## Open questions

These are tracked here until decided, then recorded in the relevant doc or ADR.

| Question | Options | Affects |
| --- | --- | --- |
| License | Private (no license), MIT, AGPL-3.0 | Root `LICENSE`, contribution terms |
| Remote access for family outside the home network | Tailscale, Synology VPN Server, reverse proxy with HTTPS | [Threat model](security/threat-model.md), [Deployment](operations/deployment.md) |
| Tree renderer | family-chart library, custom D3 + ELK layout | [Tech stack](architecture/tech-stack.md) |
