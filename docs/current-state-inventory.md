# Current State Inventory

## Objective

This document captures the current observed state of the infrastructure and will be updated progressively during the audit.

---

## 1. Hosting

### Provider
- AWS Lightsail / AWS-hosted VM context

### Instance
- `t3.nano`

### OS
- Ubuntu 22.04.5 LTS
- Kernel: `6.8.0-1044-aws`

### Notes
- Existing server is kept for V1
- Machine is small and resource-constrained

---

## 2. Resources

### CPU
- 2 vCPU

### Memory
- 416 MiB RAM

### Swap
- 2.0 GiB
- 434 MiB used at audit time

### Disk
- Root disk: 20G
- Approx. 8.2G available at audit time

### Notes
- Memory is limited
- Existing unnecessary workloads should be reviewed and reduced

---

## 3. Reverse Proxy

### Current tool
- Nginx Proxy Manager

### Container
- `proxy-app-1`

### Image
- `jc21/nginx-proxy-manager:latest`

### Status
- Running

### Notes
- Kept for V1
- Admin exposure must be reviewed because port 81 is currently publicly exposed

---

## 4. DNS

### Provider
- Cloudflare

### Domain
- `camilleaubert.com`

### Canonical target
- `camilleaubert.com`

### Redirect target
- `www.camilleaubert.com` → `camilleaubert.com`

### Notes
- DNS inventory still to be documented
- Cloudflare proxy and SSL mode still to be confirmed

---

## 5. SSL

### Current status
- Browser indicates the site is not fully secure

### Target
- Cloudflare SSL mode: Full (strict)

### Notes
- Exact issue still to be diagnosed
- Nginx Proxy Manager host configuration must be reviewed

---

## 6. Application hosting

### Portfolio app
- Container: `portfolio-astro`
- Image: `portfolio-portfolio:latest`
- Public port mapping: `8082 -> 80`

### Travel Planner app
- Container: `travel-planner-app`
- Image: `travel-planner-app:latest`
- Public port mapping: `8080 -> 80`

### Travel Planner database
- Container: `travel-planner-db`
- Image: `mysql:8.4`

### Compose projects
- `portfolio`
- `proxy`
- `travel-planner`

### Deployment strategy
- Docker Compose currently in use on server
- Compose files located under:
  - `/home/ubuntu/apps/portfolio/docker-compose.yml`
  - `/home/ubuntu/apps/proxy/docker-compose.yml`
  - `/home/ubuntu/apps/travel-planner/docker-compose.yml`

### Notes
- The current server still hosts Travel Planner components
- This should be reviewed as part of cleanup and resource optimization

---

## 7. Public exposure

### Publicly listening ports observed
- 22
- 80
- 81
- 443
- 8080
- 8082

### Expected public ports for target V1
- 80
- 443
- SSH access should remain controlled

### Main concerns
- Port 81 appears publicly exposed and likely corresponds to Nginx Proxy Manager admin
- Port 8080 exposes Travel Planner directly
- Port 8082 exposes Portfolio directly

### Principle
Only necessary public entry points should remain exposed.

---

## 8. Future extensibility

### Goal
Prepare for future subdomains without activating them yet.

### Examples
- `lab.camilleaubert.com`
- `bento.camilleaubert.com`
- future side projects

### Notes
- Future routing model must stay compatible with current proxy setup

---

## 9. Audit notes

### Observed active containers
- `proxy-app-1`
- `portfolio-astro`
- `travel-planner-app`
- `travel-planner-db`

### Observed images present
- `jc21/nginx-proxy-manager:latest`
- `mariadb:10.11`
- `mysql:8.4`
- `php:8.4-apache`
- `portfolio-portfolio:latest`
- `redis:alpine`
- `travel-planner-app:latest`

### Immediate priorities
- review Nginx Proxy Manager admin exposure
- review whether Travel Planner should remain active
- review direct exposure of ports 8080 and 8082
- inspect Cloudflare SSL mode
- inspect Nginx Proxy Manager host mappings