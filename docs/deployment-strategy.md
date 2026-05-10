# Deployment Strategy

## Objective

Define the deployment model for `camilleaubert.com`.

## Source of truth
- GitHub is the single source of truth.

## Deployment model
Recommended deployment flow:
1. code is pushed to `main`
2. CI runs basic checks
3. Docker image is built in GitHub Actions
4. image is pushed to GHCR
5. server connects and pulls latest image
6. portfolio container is restarted
7. health is verified manually at first

## Why this approach
This model is preferred because it:
- keeps the server lightweight
- avoids building on the server
- stays simple and reproducible
- supports quick redeploys
- fits the current one-site architecture

## V1 constraints
- no staging
- no advanced rollback system
- no multi-environment matrix
- no complex branching model

## Operational principle
Deployment should remain:
- automated,
- understandable,
- documented,
- SSH-based,
- easy to resume after a break.

## Current observed gap

The current runtime still exposes application containers directly through host ports (`8080`, `8082`) in addition to the reverse proxy.

Target V1 should progressively move toward:
- public exposure through reverse proxy only,
- removal of unnecessary direct host port exposure,
- clearer separation between active production services and legacy services.