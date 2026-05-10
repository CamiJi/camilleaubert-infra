# Deployment Checklist

Use this checklist for each production deployment of `camilleaubert.com`.

## 1. Local validation

- [ ] Work from the latest local version of `CamiJi/camilleaubert.com`
- [ ] Use Linux Node/npm inside WSL, not Windows binaries
- [ ] `npm install` completes successfully
- [ ] `npm run build` completes successfully
- [ ] Main pages have been reviewed locally
- [ ] The site is no longer showing the placeholder homepage

Helpful checks:

```bash
which node
which npm
node -v
npm -v
cd ~/dev/camilleaubert/camilleaubert.com
npm install
npm run build
```

## 2. Sync application files to the server

- [ ] `src/` synced
- [ ] `public/` synced
- [ ] `package.json` synced
- [ ] `package-lock.json` synced
- [ ] `astro.config.mjs` synced

Recommended commands:

```bash
rsync -avz ./src/ camille-prod:/home/ubuntu/apps/portfolio/src/
rsync -avz ./public/ camille-prod:/home/ubuntu/apps/portfolio/public/
rsync -avz ./package.json camille-prod:/home/ubuntu/apps/portfolio/
rsync -avz ./package-lock.json camille-prod:/home/ubuntu/apps/portfolio/
rsync -avz ./astro.config.mjs camille-prod:/home/ubuntu/apps/portfolio/
```

## 3. Verify server-side deployment files

- [ ] `docker-compose.yml` exists in `/home/ubuntu/apps/portfolio`
- [ ] `Dockerfile` exists in `/home/ubuntu/apps/portfolio`
- [ ] `docker compose config` is valid

Commands:

```bash
ssh camille-prod "cd /home/ubuntu/apps/portfolio && ls -la"
ssh camille-prod "cd /home/ubuntu/apps/portfolio && docker compose config"
```

## 4. Rebuild and restart production

- [ ] Docker rebuild completes successfully
- [ ] `portfolio-astro` is recreated or restarted successfully

Command:

```bash
ssh camille-prod "cd /home/ubuntu/apps/portfolio && docker compose up -d --build"
```

Successful signs include:

- `Image portfolio-portfolio Built`
- `Container portfolio-astro Recreated`
- `Container portfolio-astro Started`

## 5. Validate containers

- [ ] `portfolio-astro` is up
- [ ] `proxy-app-1` is up

Command:

```bash
ssh camille-prod 'docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"'
```

## 6. Validate public HTTP behavior

- [ ] apex HTTP redirects to HTTPS
- [ ] apex HTTPS returns `200`
- [ ] `www` redirects correctly

Commands:

```bash
curl -I http://camilleaubert.com
curl -k -I https://camilleaubert.com
curl -I http://www.camilleaubert.com
curl -k -I https://www.camilleaubert.com
```

## 7. Validate content in browser

- [ ] Homepage shows the new version
- [ ] No placeholder `Site en construction (AstroJS)` content remains
- [ ] About page is correct
- [ ] Projects page is correct
- [ ] Contact page is correct
- [ ] Responsive rendering is acceptable
- [ ] Hard refresh has been tested

Optional command-line check:

```bash
curl -k https://camilleaubert.com | head -n 40
```

## 8. If something is wrong

### Old placeholder still visible

```bash
ssh camille-prod "grep -Rni 'en construction' /home/ubuntu/apps/portfolio"
ssh camille-prod "sed -n '1,220p' /home/ubuntu/apps/portfolio/src/pages/index.astro"
```

### Missing Compose file

If `docker compose` reports no configuration file, restore:

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

### Docker build OOM

Ensure the server Dockerfile uses:

- `COPY package.json package-lock.json ./`
- `ENV NODE_OPTIONS=--max-old-space-size=512`
- `RUN npm ci`