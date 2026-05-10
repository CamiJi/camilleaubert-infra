# Infrastructure Overview

## Hosting
- Single Ubuntu server hosted on AWS
- Reverse proxy managed with Nginx Proxy Manager in Docker
- Public website served by an Astro application container
- DNS managed in Cloudflare

## Active runtime
- `proxy-app-1` → Nginx Proxy Manager
- `portfolio-astro` → Portfolio application

## Public entrypoints
- `80/tcp` → HTTP
- `443/tcp` → HTTPS
- `22/tcp` → SSH

## Internal routing
- Nginx Proxy Manager forwards `camilleaubert.com` to `portfolio-astro:80`
- `www.camilleaubert.com` permanently redirects to `https://camilleaubert.com`

## Current domain behavior
- `http://camilleaubert.com` → redirects to `https://camilleaubert.com/`
- `https://camilleaubert.com` → portfolio website
- `http://www.camilleaubert.com` → redirects to `https://camilleaubert.com`
- `https://www.camilleaubert.com` → redirects to `https://camilleaubert.com`

## Legacy cleanup completed
- Travel Planner runtime stopped and removed from active containers
- Direct public exposure on ports `8080` and `8082` removed

## Notes
- Nginx Proxy Manager admin is available on port `81` from the host and accessed through an SSH tunnel
- No dedicated DNS record remains for Travel Planner
