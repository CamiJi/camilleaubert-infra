# Infrastructure Overview

## Objective

This document describes the current and target infrastructure used to host `camilleaubert.com`.

## Current direction

The platform is currently based on:
- AWS-hosted Ubuntu server
- Cloudflare for DNS and edge layer
- Nginx Proxy Manager for reverse proxy
- Docker Compose for service orchestration on the server
- GitHub as source of truth

## Current observed runtime

### Active compose projects
- `proxy`
- `portfolio`
- `travel-planner`

### Active containers
- `proxy-app-1`
- `portfolio-astro`
- `travel-planner-app`
- `travel-planner-db`

### Publicly listening ports observed
- 22
- 80
- 81
- 443
- 8080
- 8082

## Main infrastructure concern

The current infrastructure appears to expose more public entry points than necessary.

In particular:
- Nginx Proxy Manager admin may be publicly exposed on port 81
- Portfolio is directly exposed on port 8082
- Travel Planner is directly exposed on port 8080

This should be reduced so that only the reverse proxy handles public web access.

## V1 target architecture

### Public flow
- Visitor
- Cloudflare
- Nginx Proxy Manager
- Portfolio container

### Main characteristics
- one main website
- one main application container
- one reverse proxy
- no staging
- no active subdomains yet
- future-ready routing model

## Infrastructure principles
- keep V1 simple
- avoid over-engineering
- reduce unnecessary public exposure
- document everything needed to resume quickly
- preserve future extensibility for subdomains
- secure public access before expanding the platform