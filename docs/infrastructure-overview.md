# Infrastructure Overview

## Current architecture
- Cloudflare for DNS and edge layer
- AWS-hosted Ubuntu server
- Docker Compose on server
- Nginx Proxy Manager as reverse proxy
- Astro portfolio application in Docker

## Runtime topology
- Public traffic enters through Nginx Proxy Manager
- Reverse proxy forwards to `portfolio-astro` on the shared Docker network
- No direct public application port is used anymore

## Active containers
- `proxy-app-1`
- `portfolio-astro`

## Retired containers
- `travel-planner-app`
- `travel-planner-db`

## HTTPS status
- `camilleaubert.com` works in HTTPS
- `www.camilleaubert.com` works in HTTPS

## V1 characteristics
- simple single-server deployment
- single reverse proxy
- single active website
- no staging environment
- future subdomains possible later