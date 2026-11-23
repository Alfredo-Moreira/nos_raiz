# Contributing to Nôs Raiz

## Branches

- `main` is always deployable.
- Work happens on short-lived branches named `<type>/<short-description>`, for example `feat/person-editor`, `fix/date-sorting`, `docs/initial-structure`.
- Merge through a pull request.

## Commit messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<optional scope>): <summary in imperative mood>
```

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `build`, `ci`.

Examples:

```
feat(tree): render descendants view
fix(events): sort "about 1890" dates before exact 1891 dates
docs(adr): record decision on PostgreSQL
```

## Documentation

- All project documentation lives in [`docs/`](docs/README.md).
- Update the relevant doc in the same pull request as the change it describes.
- User-visible changes get an entry under `[Unreleased]` in [CHANGELOG.md](CHANGELOG.md).

## Architecture decisions

Significant technical decisions are recorded as Architecture Decision Records (ADRs).

1. Copy [`docs/architecture/decisions/template.md`](docs/architecture/decisions/template.md) to the next number, for example `0006-short-title.md`.
2. Fill it in with status `Proposed`, and add it to the [ADR index](docs/architecture/decisions/README.md) with the same status.
3. Open a pull request; discussion happens there.
4. Before merging, set the status to `Accepted` (or `Rejected`) in both the ADR and the index.

Accepted ADRs are never edited in substance. To change a decision, write a new ADR that supersedes the old one, and mark the old one `Superseded by ADR-NNNN`.

## Privacy

Never commit real family data (names, dates, photos of living people) to the repository, including in tests, fixtures or screenshots. Use invented sample data.
