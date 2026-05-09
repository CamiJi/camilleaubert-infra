# Network and DNS

## Objective

Document the current and target DNS and public routing setup for `camilleaubert.com`.

## Canonical domain

Recommended canonical domain:
- `camilleaubert.com`

Redirect:
- `www.camilleaubert.com` → `camilleaubert.com`

## DNS provider
- Cloudflare

## Requirements
- apex domain must resolve correctly
- `www` must resolve correctly
- redirect strategy must be explicit
- future subdomain routing must be documented
- public records must remain understandable and maintainable

## Future subdomain model
The infrastructure should support future subdomains such as:
- `lab.camilleaubert.com`
- `bento.camilleaubert.com`
- other future side projects

These are not active scope for V1, but the routing model must remain compatible.

## Audit checklist
To complete:
- list existing DNS records
- identify which records are actually used
- verify apex and www routing
- verify proxy behavior at the edge
- verify intended future subdomain pattern