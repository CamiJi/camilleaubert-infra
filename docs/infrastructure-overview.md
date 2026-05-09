# Infrastructure Overview

## Objective

This document describes the current and target infrastructure used to host `camilleaubert.com`.

## Current direction

The platform is based on:
- AWS-hosted server
- Cloudflare for DNS and edge layer
- Nginx Proxy Manager for reverse proxy and subdomain routing readiness
- Docker for packaging and deployment consistency
- GitHub as source of truth
- GitHub Actions for deployment automation

## V1 target architecture

### Public flow
- Visitor
- Cloudflare
- Nginx Proxy Manager
- Portfolio container

### Main characteristics
- one main website
- one main application container
- one existing reverse proxy
- no staging
- no active subdomains yet
- future-ready routing model

## Infrastructure principles
- keep V1 simple
- avoid over-engineering
- document everything needed to resume quickly
- preserve future extensibility for subdomains
- secure public access before expanding the platform