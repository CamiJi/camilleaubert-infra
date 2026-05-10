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
- [`INFRASTRUCTURE.md`](./INFRASTRUCTURE.md)

## Operations runbook

### Accès serveur
```bash
ssh ubuntu@camilleaubert.com
```

### Accès à l’admin Nginx Proxy Manager via tunnel SSH
```bash
ssh -L 8081:127.0.0.1:81 ubuntu@camilleaubert.com
```
Puis ouvrir :
- http://127.0.0.1:8081

### Emplacements des stacks
- Proxy: `/home/ubuntu/apps/proxy`
- Portfolio: `/home/ubuntu/apps/portfolio`
- Legacy stoppé: `/home/ubuntu/apps/travel-planner`

### Conteneurs actifs attendus
- `proxy-app-1`
- `portfolio-astro`

### Vérifier l’état des conteneurs
```bash
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"
```

### Redémarrer le portfolio
```bash
cd /home/ubuntu/apps/portfolio
docker compose up -d --build
```

### Redémarrer le proxy
```bash
cd /home/ubuntu/apps/proxy
docker compose up -d
```

### Arrêter une stack
#### Portfolio
```bash
cd /home/ubuntu/apps/portfolio
docker compose down
```

#### Proxy
```bash
cd /home/ubuntu/apps/proxy
docker compose down
```

### Vérifications HTTP/HTTPS
```bash
curl -I http://camilleaubert.com
curl -k -I https://camilleaubert.com
curl -I http://www.camilleaubert.com
curl -k -I https://www.camilleaubert.com
```

### Comportement attendu des domaines
- `http://camilleaubert.com` → redirection vers `https://camilleaubert.com/`
- `https://camilleaubert.com` → portfolio en 200
- `http://www.camilleaubert.com` → redirection vers `https://camilleaubert.com`
- `https://www.camilleaubert.com` → redirection vers `https://camilleaubert.com`

### Logs utiles
#### Portfolio
```bash
docker logs --tail 100 portfolio-astro
```

#### Proxy
```bash
docker logs --tail 100 proxy-app-1
```

### Réseau
Le portfolio n’expose plus de port public dédié.
Le routage se fait uniquement via Nginx Proxy Manager vers `portfolio-astro:80`.

### Ports publics attendus
- `22` SSH
- `80` HTTP
- `443` HTTPS

## Guiding principle

The infrastructure must stay:
- simple,
- documented,
- secure enough,
- future-ready for subdomains,
- and easy to resume after a break.
