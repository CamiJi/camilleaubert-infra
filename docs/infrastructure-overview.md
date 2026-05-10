# Infrastructure Overview

## Current production architecture

- Single Ubuntu server on AWS
- Cloudflare handles DNS and public domain routing
- Nginx Proxy Manager runs in Docker as the public reverse proxy
- The portfolio is served by a dedicated Docker container
- Shared Docker network: `travel-network`

## Runtime topology

Public traffic flow:

1. client request reaches Cloudflare
2. traffic resolves to the AWS Ubuntu server
3. Nginx Proxy Manager receives the request on ports 80/443
4. Nginx Proxy Manager routes traffic to `portfolio-astro:80`
5. the portfolio container serves the generated Astro site via Nginx

## Active containers

Expected active containers:

- `proxy-app-1`
- `portfolio-astro`

## Important server paths

- Proxy stack: `/home/ubuntu/apps/proxy`
- Portfolio stack: `/home/ubuntu/apps/portfolio`

## Public ports

Expected public ports on the host:

- `22` for SSH
- `80` for HTTP
- `81` for Nginx Proxy Manager admin access (normally via SSH tunnel only)
- `443` for HTTPS

## Domains

Current public domains:

- `camilleaubert.com`
- `www.camilleaubert.com`

Expected behavior:

- `http://camilleaubert.com` redirects to `https://camilleaubert.com/`
- `https://camilleaubert.com` returns the portfolio
- `http://www.camilleaubert.com` redirects to apex
- `https://www.camilleaubert.com` redirects to apex

## Deployment model reality

The current portfolio deployment is **not** a git-based deploy on the server.

The directory `/home/ubuntu/apps/portfolio` is currently a server-side working directory rebuilt locally with Docker Compose. It is not a Git clone.

This means the effective deployment flow is:

1. validate locally
2. sync application files to the server
3. verify deployment files exist on the server
4. run Docker Compose rebuild on the server
5. validate containers and HTTP behavior

## V1 characteristics

- simple single-server deployment
- single reverse proxy
- one active website
- no staging environment
- manual but understandable deployment process

## Future improvements

Potential future improvements:

- version deployment files explicitly in Git
- move to a more reproducible deployment workflow
- add helper scripts for sync and rebuild
- optionally move to image-based CI/CD later