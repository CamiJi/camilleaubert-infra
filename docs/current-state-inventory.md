# Current State Inventory

## Objective

This document captures the current observed state of the infrastructure and will be updated progressively during the audit.

---

## 1. Hosting

### Provider
- AWS

### Instance
- To be documented

### OS
- To be documented

### Notes
- Existing server is kept for V1

---

## 2. Reverse Proxy

### Current tool
- Nginx Proxy Manager

### Status
- Already configured

### Notes
- Kept for V1
- Must be audited for SSL, routing, and public admin exposure

---

## 3. DNS

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

---

## 4. SSL

### Current status
- Browser indicates the site is not fully secure

### Target
- Cloudflare SSL mode: Full (strict)

### Notes
- Exact issue still to be diagnosed

---

## 5. Application hosting

### Portfolio app
- To be documented

### Container strategy
- One main portfolio container planned for V1

### Deployment strategy
- GitHub Actions + Docker image + GHCR + SSH deployment

### Notes
- Current runtime state still to be audited

---

## 6. Public exposure

### Expected public ports
- 80
- 443

### Expected admin access
- SSH only
- Nginx Proxy Manager admin exposure must be reviewed

---

## 7. Future extensibility

### Goal
Prepare for future subdomains without activating them yet.

### Examples
- `lab.camilleaubert.com`
- `bento.camilleaubert.com`
- future side projects

### Notes
- Future routing model must stay compatible with current proxy setup

---

## 8. Audit notes

### To fill during audit
- active containers
- open ports
- DNS records
- Cloudflare proxy mode
- SSL chain details
- NPM host mappings
- obsolete services