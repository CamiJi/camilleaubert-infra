# Repository Map

## Purpose
This document explains how the `camilleaubert-infra` repository is organized and where to add or find infrastructure knowledge.

## Top-level files
- `README.md` → entry point, summary, and runbook
- `INFRASTRUCTURE.md` → current live infrastructure snapshot

## Recommended documentation structure
### `docs/infrastructure-overview.md`
High-level architecture, runtime topology, active services, and hosting model.

### `docs/repository-map.md`
This file. Explains the purpose of each documentation file and how the repository is structured.

### `docs/network-and-dns.md`
DNS records, public entrypoints, domain behavior, Cloudflare usage, and canonical domain policy.

### `docs/ssl-and-exposure.md`
HTTPS status, certificate handling, exposed ports, reverse proxy rules, and hardening notes.

### `docs/deployment-checklist.md`
Step-by-step checklist to deploy or redeploy infra and validate expected behavior.

### `docs/portfolio-operations.md`
Site-specific operational notes for the portfolio app, including restart, checks, and logs.

## Documentation principles
- Keep files short and practical.
- Prefer current-state documentation over vague plans.
- Update docs when infrastructure changes.
- Record real commands that are known to work.
- Separate high-level overview from operational procedures.