# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Project documentation: product vision, MVP scope, roadmap, architecture, data model, tech stack, ADRs 0001–0005, security and operations docs.
- ADRs 0006–0010: monorepo with Docker-only development, REST API, OAuth-only sign-in, per-tree roles with a protected view, MVP on the home NAS.
- `LICENSE`: all rights reserved.

### Changed

- Hosting: the MVP targets the home UGREEN NAS behind a Cloudflare tunnel (ADR-0010 supersedes ADR-0005).
- Security model: Google sign-in, invite-only; per-tree owner/editor/viewer roles; protected view by default; share links.
- Backups: now an open question owned by the NAS owner.
