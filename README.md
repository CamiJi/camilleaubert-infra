# Camille Aubert infrastructure

This repository documents and supports the production infrastructure for the Camille Aubert portfolio and related services.

## Scope

This repo is the infrastructure reference for:

- production hosting topology
- reverse proxy setup and conventions
- deployment strategy and checklist
- operational notes for server-side services

Application-specific code for the portfolio lives in:

- `CamiJi/camilleaubert.com`

## Current production architecture

- Single Ubuntu server on AWS
- Cloudflare handles DNS
- Nginx Proxy Manager runs in Docker and exposes ports 80/81/443
- The portfolio is served by a dedicated Docker container
- Docker network used for shared service connectivity: `travel-network`

## Important server paths

- Proxy stack: `/home/ubuntu/apps/proxy`
- Portfolio stack: `/home/ubuntu/apps/portfolio`

## Current deployment reality

The current portfolio deployment is not a git-based deploy on the server.

The directory `/home/ubuntu/apps/portfolio` is a server-side working directory rebuilt locally with Docker Compose. It is currently not a Git clone, so `git pull` is not part of the operational workflow.

Current real-world flow:

1. prepare and validate code locally
2. sync application files to the server
3. verify deployment files exist on the server
4. rebuild with Docker Compose
5. validate containers and HTTP routing

## Documentation map

- `docs/infrastructure-overview.md`
- `docs/deployment-strategy.md`
- `docs/deployment-checklist.md`

## Recommended SSH access

Use the configured SSH alias:

```bash
ssh camille-prod
```

## Related application repo

For application build, content changes, Astro structure, and app-side runbooks, see:

- `CamiJi/camilleaubert.com`
- `CamiJi/camilleaubert.com/docs/deployment-runbook.md`
- `CamiJi/camilleaubert.com/docs/operations.md`

## Setup depuis un nouvel ordi

Ce dépôt contient le workspace VS Code multi-root qui référence les deux repos. Pour tout reconstruire depuis zéro :

### 1. Cloner les deux repos côte à côte

```bash
mkdir -p ~/dev/camilleaubert && cd ~/dev/camilleaubert
git clone https://github.com/CamiJi/camilleaubert-infra.git
git clone https://github.com/CamiJi/camilleaubert.com.git
```

Structure résultante :
```
~/dev/camilleaubert/
├── camilleaubert.com/       ← repo application (Astro)
└── camilleaubert-infra/     ← repo infrastructure (vous êtes ici)
```

### 2. Ouvrir le workspace

```bash
cd ~/dev/camilleaubert/camilleaubert-infra
code camilleaubert.code-workspace
```

Le workspace charge automatiquement les deux repos en multi-root.

### 3. Configurer SSH

```bash
chmod 600 ~/.ssh/lightsail-eu-west-3.pem
```

Ajouter dans `~/.ssh/config` :
```sshconfig
Host camille-prod
  HostName 13.39.194.192
  User ubuntu
  IdentityFile ~/.ssh/lightsail-eu-west-3.pem
  IdentitiesOnly yes
```

### 4. Installer les dépendances applicatives

```bash
cd ~/dev/camilleaubert/camilleaubert.com
npm install
```

### 5. VS Code Copilot : tout est prêt

Le workspace inclut :
- Les **deux repos** en multi-root (portfolio + infra)
- Le **skill de déploiement** Copilot dans `.github/skills/deploy/SKILL.md` (slash command `/deploy`)
- La **repository memory** (chargée automatiquement par Copilot)

## VS Code Copilot skills

Ce dépôt contient un skill Copilot pour le déploiement :

- `.github/skills/deploy/SKILL.md` — procédure complète de déploiement

Invoquer avec `/deploy` dans le chat Copilot, ou simplement demander "on redéploie ?".
