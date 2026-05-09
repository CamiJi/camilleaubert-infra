# Repository Map

## Repositories in scope
- `CamiJi/camilleaubert.com`
- `CamiJi/camilleaubert-infra`

## Role of `camilleaubert-infra`
This repository documents and governs:
- infrastructure topology,
- hosting model,
- DNS,
- SSL,
- reverse proxy exposure,
- deployment operations,
- server-side operational knowledge.

## Role of `camilleaubert.com`
This repository governs:
- product scope,
- portfolio application,
- design and content direction,
- frontend implementation,
- site delivery workflows.

## Boundary rule

### Belongs here
- server inventory
- port exposure
- Cloudflare configuration notes
- canonical domain rules
- SSL state
- deployment steps
- operational documentation
- future subdomain routing model

### Does not belong here
- Astro pages
- UI components
- copywriting
- visual design rationale
- frontend assets