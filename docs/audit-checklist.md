# Infrastructure Audit Checklist

## Objective

This checklist is used to audit the current infrastructure behind `camilleaubert.com` before making further architectural or deployment changes.

The goal is to:
- understand what is currently running,
- identify what is actually used,
- detect security and SSL issues,
- document the real traffic flow,
- establish a clean baseline for future work.

---

## 1. Server inventory

- [ ] Identify the hosting provider and server instance details
- [ ] Record the operating system and version
- [ ] Record available CPU / RAM / disk information
- [ ] List all running Docker containers
- [ ] List all Docker images currently present
- [ ] Identify which containers are actively used
- [ ] Identify which containers are obsolete or experimental
- [ ] Confirm whether Nginx Proxy Manager is running
- [ ] Confirm whether the portfolio container already exists
- [ ] Record current restart policy for each relevant container

---

## 2. Public exposure

- [ ] List all publicly exposed ports
- [ ] Confirm whether only 80 and 443 should be public
- [ ] Check whether Nginx Proxy Manager admin is exposed publicly
- [ ] Check whether SSH is publicly exposed
- [ ] Identify any unnecessary open ports
- [ ] Document current firewall / security group situation

---

## 3. DNS and Cloudflare

- [ ] List all DNS records related to `camilleaubert.com`
- [ ] Identify the current A / AAAA / CNAME records
- [ ] Confirm how apex domain is routed
- [ ] Confirm how `www` is routed
- [ ] Check whether Cloudflare proxy is enabled or disabled for each record
- [ ] Confirm whether there are existing subdomain records
- [ ] Identify obsolete DNS entries
- [ ] Document intended canonical domain behavior

---

## 4. SSL and HTTPS

- [ ] Confirm current browser SSL warning behavior
- [ ] Inspect the certificate served to the browser
- [ ] Confirm whether Cloudflare SSL mode is Off / Flexible / Full / Full (strict)
- [ ] Confirm whether an origin certificate exists
- [ ] Confirm whether Nginx Proxy Manager uses a valid certificate upstream
- [ ] Check whether apex domain works in HTTPS
- [ ] Check whether `www` works in HTTPS
- [ ] Check whether HTTP redirects cleanly to HTTPS
- [ ] Check whether there are mixed-content warnings
- [ ] Document exact SSL remediation path

---

## 5. Reverse proxy mapping

- [ ] List all hosts configured in Nginx Proxy Manager
- [ ] Identify which host corresponds to `camilleaubert.com`
- [ ] Record target upstream IP / container / port
- [ ] Record whether Force SSL is enabled
- [ ] Record whether HTTP/2 or advanced options are used
- [ ] Record any custom Nginx rules in place
- [ ] Record whether `www` and apex are both configured
- [ ] Confirm future readiness for subdomain routing

---

## 6. Docker and deployment

- [ ] Identify how the current site is deployed
- [ ] Confirm whether deployment is manual or automated
- [ ] Confirm whether GitHub Actions is already used
- [ ] Confirm whether GHCR is already used
- [ ] Identify whether builds occur on the server or elsewhere
- [ ] Identify the current source path or container mapping for the site
- [ ] Record current deployment commands
- [ ] Record rollback method if any exists

---

## 7. Current application state

- [ ] Identify what currently serves `camilleaubert.com`
- [ ] Confirm whether the current website is static / Astro / placeholder / other
- [ ] Identify current domain behavior in browser
- [ ] Identify whether any old experiments remain connected
- [ ] Identify whether Travel Planner artifacts still exist on the server
- [ ] Confirm what should be kept vs removed later

---

## 8. Documentation outputs expected from this audit

At the end of the audit, the following must be documented:
- real infrastructure flow
- real DNS mapping
- real SSL status
- real exposed ports
- real Nginx Proxy Manager mapping
- relevant containers
- obsolete containers
- immediate remediation priorities