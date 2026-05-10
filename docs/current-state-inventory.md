# Current State Inventory

## Hosting
- AWS-hosted Ubuntu server
- Ubuntu 22.04.5 LTS
- Instance type: `t3.nano`

## Runtime resources observed
- 2 vCPU
- 416 MiB RAM
- 2 GiB swap
- Root disk: 20G

## Active services
- `proxy-app-1` → Nginx Proxy Manager
- `portfolio-astro` → Portfolio application

## Removed legacy services
- `travel-planner-app`
- `travel-planner-db`

## Reverse proxy
- Nginx Proxy Manager in Docker
- Admin reachable through SSH tunnel
- Mounted paths:
  - `/home/ubuntu/apps/proxy/data`
  - `/home/ubuntu/apps/proxy/letsencrypt`

## Portfolio routing
- Forwarded by Nginx Proxy Manager to:
  - `http://portfolio-astro:80`

## Domains currently working
- `https://camilleaubert.com`
- `https://www.camilleaubert.com`

## Public port exposure status
### Intended public web entrypoints
- `80`
- `443`

### Remaining mapped admin port
- `81` mapped by Docker for Nginx Proxy Manager admin
- currently not reachable publicly from outside during audit
- admin access performed via SSH tunnel

### Removed direct app exposure
- `8080` removed from active runtime
- `8082` removed from active runtime

## Notes
- Apex and www HTTPS now work correctly
- Legacy Travel Planner runtime has been stopped
- Portfolio is now accessed through the reverse proxy only
- Canonical domain redirection still to be decided