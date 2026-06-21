---
name: deploy
description: 'Déploiement du site camilleaubert.com en production. Use when: deploying, redeploying, pushing to production, mise en ligne, déployer, redéployer, ship to prod, rsync to server, docker compose build on prod.'
argument-hint: '[--validate-only] Vérifier sans déployer'
user-invocable: true
---

# Déploiement camilleaubert.com

Déploie le portfolio Astro sur le serveur AWS de production via rsync + Docker Compose.

## Architecture cible

| Élément | Valeur |
|---|---|
| Serveur | Ubuntu AWS Lightsail, IP `13.39.194.192` |
| DNS | Cloudflare |
| Reverse Proxy | Nginx Proxy Manager (Docker, `proxy-app-1`) |
| Site | Astro + Nginx (Docker, `portfolio-astro`) |
| Réseau | `travel-network` (externe, partagé) |
| SSH | `ssh camille-prod` (user `ubuntu`, clé `~/.ssh/lightsail-eu-west-3.pem`) |

## Procédure de déploiement

### Étape 1 — Valider en local

```bash
cd ~/dev/camilleaubert/camilleaubert.com
npm install && npm run build
```

Sortie attendue : `5 page(s) built`.

### Étape 2 — Synchroniser les fichiers vers le serveur

```bash
cd ~/dev/camilleaubert/camilleaubert.com
rsync -avz ./src/ camille-prod:/home/ubuntu/apps/portfolio/src/
rsync -avz ./public/ camille-prod:/home/ubuntu/apps/portfolio/public/
rsync -avz ./package.json camille-prod:/home/ubuntu/apps/portfolio/
rsync -avz ./package-lock.json camille-prod:/home/ubuntu/apps/portfolio/
rsync -avz ./astro.config.mjs camille-prod:/home/ubuntu/apps/portfolio/
```

> ⚠️ **CRITIQUE** : Ne jamais faire de sync destructive globale (`rsync --delete` sur tout le dossier). `docker-compose.yml` et `Dockerfile` existent **uniquement sur le serveur**, pas dans le repo local. Les perdre casse le déploiement.

### Étape 3 — Vérifier les fichiers serveur

```bash
ssh camille-prod "cd /home/ubuntu/apps/portfolio && ls -la"
ssh camille-prod "cd /home/ubuntu/apps/portfolio && docker compose config"
```

Vérifier la présence de : `docker-compose.yml`, `Dockerfile`.

### Étape 4 — Rebuild Docker sur le serveur

```bash
ssh camille-prod "cd /home/ubuntu/apps/portfolio && docker compose up -d --build"
```

Signaux de succès :
- `Image portfolio-portfolio Built`
- `Container portfolio-astro Recreated`
- `Container portfolio-astro Started`

### Étape 5 — Valider la production

```bash
# Conteneurs actifs
ssh camille-prod 'docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"'

# Comportement HTTP
curl -I http://camilleaubert.com          # → 301 HTTPS
curl -kI https://camilleaubert.com        # → 200
curl -I http://www.camilleaubert.com      # → 301 apex
curl -kI https://www.camilleaubert.com    # → 301 apex
```

Conteneurs attendus : `portfolio-astro` (Up), `proxy-app-1` (Up).

## Chemins serveur

| Chemin | Contenu |
|---|---|
| `/home/ubuntu/apps/portfolio/` | Code applicatif + Docker |
| `/home/ubuntu/apps/proxy/` | Nginx Proxy Manager |

## Fichiers canoniques (existent uniquement sur le serveur)

### docker-compose.yml

```yaml
services:
  portfolio:
    build: .
    container_name: portfolio-astro
    restart: unless-stopped
    networks:
      - travel-network

networks:
  travel-network:
    external: true
```

### Dockerfile

```dockerfile
FROM node:22.12-alpine AS builder
WORKDIR /app
COPY package.json package-lock.json ./
ENV NODE_OPTIONS=--max-old-space-size=512
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Repos associés

- Application : `CamiJi/camilleaubert.com` (`~/dev/camilleaubert/camilleaubert.com`)
- Infrastructure : `CamiJi/camilleaubert-infra` (`~/dev/camilleaubert/camilleaubert-infra`)
