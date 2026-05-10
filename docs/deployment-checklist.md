# Deployment Checklist

## Before deployment
- Confirm SSH access to the server
- Confirm Docker is running
- Confirm the target repo branch is up to date
- Confirm the portfolio container can be rebuilt
- Confirm current public domains are:
  - `camilleaubert.com`
  - `www.camilleaubert.com`

## Deploy portfolio
```bash
ssh ubuntu@camilleaubert.com
cd /home/ubuntu/apps/portfolio
docker compose up -d --build
```

## Validate containers
```bash
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"
```

Expected active containers:
- `proxy-app-1`
- `portfolio-astro`

## Validate HTTP/HTTPS behavior
```bash
curl -I http://camilleaubert.com
curl -k -I https://camilleaubert.com
curl -I http://www.camilleaubert.com
curl -k -I https://www.camilleaubert.com
```

Expected behavior:
- `http://camilleaubert.com` → redirect to `https://camilleaubert.com/`
- `https://camilleaubert.com` → `200`
- `http://www.camilleaubert.com` → redirect to `https://camilleaubert.com`
- `https://www.camilleaubert.com` → redirect to `https://camilleaubert.com`

## If something is wrong
### Check portfolio logs
```bash
docker logs --tail 100 portfolio-astro
```

### Check proxy logs
```bash
docker logs --tail 100 proxy-app-1
```

### Check port exposure
```bash
ss -tulpn
```

## Post-deployment checks
- Homepage loads correctly
- HTTPS certificate is valid
- `www` redirects to apex
- No direct public app port is exposed
- Nginx Proxy Manager still routes to `portfolio-astro:80`