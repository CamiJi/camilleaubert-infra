# SSL and Public Exposure

## Objective

Track the current SSL situation and the public exposure/security posture of the platform.

## Current known issue
The website currently appears as **not fully secure** in the browser.

This makes SSL remediation a top infrastructure priority.

## Target SSL model

Recommended target:
- Cloudflare SSL mode: **Full (strict)**

Reason:
- clean edge-to-origin trust chain
- fewer redirect inconsistencies
- stronger long-term setup
- better production baseline

## Public exposure review
Must be audited:
- which ports are publicly exposed
- whether Nginx Proxy Manager admin is accessible publicly
- whether HTTP and HTTPS routing are both correct
- whether apex and www behave consistently
- whether redirects are clean

## Immediate checks required
- browser certificate chain
- Cloudflare SSL configuration
- origin certificate validity
- NPM host mapping
- public access to admin endpoints
- exposure of non-essential ports

## Principle
Before adding future subdomains, public HTTPS and exposure must be clean, stable, and documented.