# camilleaubert-infra

Infrastructure repository for the `camilleaubert.com` platform.

## Purpose

This repository contains the infrastructure and operations documentation required to host, secure, expose, and evolve the Camille Aubert web platform.

It is the source of truth for:
- infrastructure audit,
- DNS mapping,
- SSL and public exposure notes,
- reverse proxy documentation,
- deployment operations,
- future subdomain hosting strategy.

## Related repositories

### Main repositories
- **Portfolio repository:** `CamiJi/camilleaubert.com`
- **Infrastructure repository:** `CamiJi/camilleaubert-infra`

## Repository responsibilities

### `camilleaubert-infra`
Contains:
- server audit,
- Cloudflare notes,
- Nginx Proxy Manager setup documentation,
- DNS and redirect rules,
- SSL status and remediation notes,
- deployment runbooks,
- operational commands,
- future subdomain routing principles.

### `camilleaubert.com`
Contains:
- Astro application source code,
- portfolio documentation,
- content strategy,
- design direction,
- project framing,
- site-level CI/CD.

## Documentation

Main documents:
- [`docs/infrastructure-overview.md`](./docs/infrastructure-overview.md)
- [`docs/repository-map.md`](./docs/repository-map.md)
- [`docs/network-and-dns.md`](./docs/network-and-dns.md)
- [`docs/ssl-and-exposure.md`](./docs/ssl-and-exposure.md)
- [`docs/deployment-strategy.md`](./docs/deployment-strategy.md)

## Guiding principle

The infrastructure must stay:
- simple,
- documented,
- secure enough,
- future-ready for subdomains,
- and easy to resume after a break.