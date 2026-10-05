# Current State Inventory

> Mis à jour le 2026-09-25 avec des valeurs relevées en SSH (`docker ps`,
> `free -m`, `df -h`, `uptime`). L'ancienne ligne « t3.nano / 416 MiB » est
> obsolète : l'instance a été **upgradée lors du déploiement de Nestor**
> (coût passé de ~5 € à ~15 €/mois, info Camille).

## Hosting

- AWS Lightsail, Ubuntu 22.04.5 LTS
- Instance upgradée (~juillet 2026, lors de la mise en place de Nestor)
  - plan ~2 GiB RAM / 2 vCPU (équivalent t3.small) — type exact à confirmer dans la console
- Coût : **~15 €/mois**

## Runtime resources observed (2026-09-25)

- 2 vCPU
- RAM : **1910 MiB** total, ~832 MiB disponibles (avant déploiement du watchdog)
- Swap : 2047 MiB (~856 MiB utilisés, 42 %)
- Disque : **58 G**, 25 G utilisés (42 %), 34 G libres
- Uptime : 95 jours, load average faible

## Active services

- `proxy-app-1` → Nginx Proxy Manager
- `portfolio-astro` → Portfolio (Astro)
- `nestor-app` → Nestor le Groom (app Laravel, déployée ~juillet 2026)
- `nestor-db` → MySQL de Nestor (healthy)

Dernière vérification : les quatre conteneurs étaient `Up` (25/09/2026).

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

- Apex et www HTTPS fonctionnent correctement
- Le runtime Travel Planner legacy est arrêté
- Le portfolio est servi via le reverse proxy uniquement
- Redirection du domaine canonique encore à décider
- Conséquence de l'upgrade pour les projets hébergés : la contrainte
  « 416 MiB ne pardonne pas » n'est plus valable — environ 0,8–1 GiB de RAM
  disponible selon la charge
  (voir `../../hermes/docs/cahier-des-charges.md` pour le projet Hermes)
- Swap observé à 42 % le 25/09 ; surveillé par le watchdog Hermes (seuil 50 %)
- **Backup Nestor en anomalie** (25/09) : le dump le plus récent date du
  12/09. Le crontab quotidien lance une redirection vers
  `/var/log/nestor-backup.log`, mais l'utilisateur `ubuntu` ne peut pas écrire
  dans `/var/log` et le fichier log est absent ; la redirection empêche
  probablement le script de démarrer. Voir `nestor/backups/` et corriger la
  destination du log séparément.
