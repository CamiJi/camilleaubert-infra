# Deployment Strategy

## Objective

Define the actual deployment model currently used for `camilleaubert.com`, while also documenting the desired target state.

## Current operational reality

The current deployment is **not**:

- a `git pull` on the server
- a fully automated CI/CD pipeline
- a GHCR-based image pull workflow

The current deployment **is**:

1. local development and validation
2. manual sync of application files to `/home/ubuntu/apps/portfolio`
3. verification of required deployment files on the server
4. local Docker rebuild on the production server
5. manual validation of containers and public HTTP behavior

## Why this matters

Earlier documentation described a target workflow based on GitHub as source of truth, CI checks, image build, registry push, and server pull.

That target may still be desirable later, but it does **not** describe the current production process accurately enough for fast recovery.

This document now prioritizes documenting the **real workflow in use today**.

## Current real deployment flow

### Step 1 — validate locally

Run local install/build checks in WSL/Linux:

```bash
cd ~/dev/camilleaubert/camilleaubert.com
npm install
npm run build
```

### Step 2 — sync application files

Sync the current app files to the server working directory:

```bash
rsync -avz ./src/ camille-prod:/home/ubuntu/apps/portfolio/src/
rsync -avz ./public/ camille-prod:/home/ubuntu/apps/portfolio/public/
rsync -avz ./package.json camille-prod:/home/ubuntu/apps/portfolio/
rsync -avz ./package-lock.json camille-prod:/home/ubuntu/apps/portfolio/
rsync -avz ./astro.config.mjs camille-prod:/home/ubuntu/apps/portfolio/
```

### Step 3 — verify deployment files

Check that the server still has:

- `docker-compose.yml`
- `Dockerfile`

and validate Compose config:

```bash
ssh camille-prod "cd /home/ubuntu/apps/portfolio && ls -la"
ssh camille-prod "cd /home/ubuntu/apps/portfolio && docker compose config"
```

### Step 4 — rebuild on the server

```bash
ssh camille-prod "cd /home/ubuntu/apps/portfolio && docker compose up -d --build"
```

### Step 5 — validate production

```bash
ssh camille-prod 'docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"'
curl -I http://camilleaubert.com
curl -k -I https://camilleaubert.com
curl -I http://www.camilleaubert.com
curl -k -I https://www.camilleaubert.com
```

## Canonical deployment files

### `docker-compose.yml`

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

### `Dockerfile`

```dockerfile
FROM node:20-alpine AS builder
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

## Known risks in the current model

### Drift risk

Because the server directory is not a Git clone, production can drift from the repository state if sync steps are incomplete.

### Deployment file loss risk

If `docker-compose.yml` is not versioned in Git and only lives on the server, it can be lost during unsafe sync operations.

### Manual process risk

The workflow is understandable, but still partially manual and therefore error-prone.

## Target future state

A better future deployment strategy would be:

1. all deployment files versioned explicitly in Git
2. one clear source of truth for infra/app boundaries
3. optional deployment helper scripts
4. optional CI/CD image build and registry-based deployment

## Guiding principle

The deployment process must remain:

- reproducible
- documented
- resumable after a break
-
