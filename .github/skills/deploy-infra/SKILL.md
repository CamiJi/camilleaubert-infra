---
name: deploy-infra
description: 'Déploiement et gestion de l''infrastructure serveur AWS partagée. Use when: upgrader instance AWS, ajouter un nouveau service/sous-domaine, configurer NPM, gérer Cloudflare DNS, déployer un nouveau projet Docker sur le serveur, snapshot/restore Lightsail, firewall AWS.'
argument-hint: '[service-name] Service à déployer (nestor, portfolio, nouveau)'
user-invocable: true
---

# Infrastructure partagée — Déploiement multi-projets

Le serveur AWS Lightsail héberge plusieurs projets Docker sur une même instance, routés par Nginx Proxy Manager et Cloudflare.

## Serveur

| Propriété | Valeur |
|---|---|
| Provider | AWS Lightsail |
| Plan | t3.small (2 vCPU, 2 Go RAM, 60 Go SSD) — ~13 $/mois |
| IP statique | `13.39.194.192` |
| Région | eu-west-3 (Paris) |
| OS | Ubuntu 22.04 |
| SSH | `ssh camille-prod` (user `ubuntu`, clé `~/.ssh/lightsail-eu-west-3.pem`) |
| Config SSH | `~/.ssh/config` → Host camille-prod |

## Architecture

```
┌─── AWS Lightsail t3.small ───────────────────────────────┐
│                                                           │
│  Ports exposés : 80, 81, 443                              │
│                                                           │
│  Cloudflare (DNS proxy)                                    │
│     ↓                                                     │
│  Nginx Proxy Manager (proxy-app-1)                        │
│     ├─ camilleaubert.com → portfolio-astro:80             │
│     ├─ nestor.camilleaubert.com → nestor-app:80           │
│     └─ travel-planner.camilleaubert.com → 172.17.0.1:8080 │
│                                                           │
│  Conteneurs Docker :                                      │
│     proxy-app-1       NPM (jc21/nginx-proxy-manager)      │
│     portfolio-astro   Astro+Nginx Alpine                  │
│     nestor-app        PHP 8.4-FPM + Nginx Alpine          │
│     nestor-db         MySQL 8.0                           │
│                                                           │
│  Réseau : travel-network (bridge externe)                 │
└───────────────────────────────────────────────────────────┘
```

## Services déployés

| Service | Sous-domaine | Conteneur(s) | Stack | Doc déploiement |
|---|---|---|---|---|
| Portfolio | camilleaubert.com | portfolio-astro | Astro+Nginx | `deploy` skill |
| Nestor | nestor.camilleaubert.com | nestor-app, nestor-db | Laravel+MySQL | `deploy-nestor` skill |
| Travel Planner | travel-planner.camilleaubert.com | — (host network) | — | legacy |

## Chemins serveur

| Projet | Chemin |
|---|---|
| Portfolio | `/home/ubuntu/apps/portfolio/` |
| Nestor | `/home/ubuntu/apps/nestor/` |
| Proxy NPM | `/home/ubuntu/apps/proxy/` |
| Travel Planner | `/home/ubuntu/apps/travel-planner/` |

## Upgrader l'instance AWS

⚠️ Procédure complète. L'IP statique doit survivre au changement.

### 1. Snapshot
Console AWS Lightsail → Instances → sélectionner l'instance → Snapshots → Create snapshot
→ Nommer `pre-upgrade-YYYYMMDD`

### 2. Créer la nouvelle instance
Onglet Snapshots → `...` → Create new instance
→ Choisir le bundle supérieur (ex: t3.small, 2 Go)
→ Nommer (ex: `camille-prod-t3small`)
→ Create

### 3. Transférer l'IP statique
⚠️ **CRITIQUE** — sans cette étape, le DNS pointe dans le vide.
Networking → IP statique `13.39.194.192` → Detach de l'ancienne instance → Attach à la nouvelle.

### 4. Nettoyer known_hosts
```bash
ssh-keygen -f ~/.ssh/known_hosts -R 13.39.194.192
```

### 5. Vérifier + firewall
```bash
ssh camille-prod "free -h && docker ps"
```

Console AWS → Instance → Networking → Vérifier les règles :
- SSH TCP 22 (0.0.0.0/0)
- HTTP TCP 80 (0.0.0.0/0)
- HTTPS TCP 443 (0.0.0.0/0) ← souvent manquant après snapshot !

## Ajouter un nouveau service

### Checklist
1. [ ] Sous-domaine dispo ? (ne pas écraser un existant)
2. [ ] RAM suffisante ? (`free -h` — garder ~500 Mo de marge)
3. [ ] Dockerfile + docker-compose.yml créés
4. [ ] Réseau `travel-network` utilisé (`docker network create travel-network` si absent)
5. [ ] `.env.production` sur le serveur (pas commité !)
6. [ ] NPM proxy host + SSL configuré
7. [ ] DNS Cloudflare ajouté
8. [ ] Firewall AWS Lightsail vérifié (port ouvert si nouveau proto)
9. [ ] Queue worker démarré (si applicable)
10. [ ] Backup/snapshot avant déploiement si critique

### Template docker-compose.yml (nouveau service)
```yaml
services:
  mon-app:
    build: .
    container_name: mon-app
    restart: unless-stopped
    env_file:
      - .env.production
    networks:
      - travel-network

networks:
  travel-network:
    external: true
```

## Nginx Proxy Manager

### Accès
- **URL admin** : port 81 (SSH tunnel requis)
- **Tunnel** : `ssh -L 8282:127.0.0.1:81 camille-prod` → http://localhost:8282
- **Email admin** : `aubertcam@gmail.com`
- **API** : `http://localhost:8282/api/` (token Bearer auth)

### Créer un proxy host + SSL (via API)

```bash
# Authentifier
TOKEN=$(curl -s http://localhost:8282/api/tokens \
  -H "Content-Type: application/json" \
  -d '{"identity":"aubertcam@gmail.com","secret":"MDP_NPM"}' \
  | grep -o '"token":"[^"]*"' | cut -d'"' -f4)

# Créer (SSL Let's Encrypt auto-généré)
curl -s -X POST http://localhost:8282/api/nginx/proxy-hosts \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "domain_names":["mon-service.camilleaubert.com"],
    "forward_scheme":"http",
    "forward_host":"mon-conteneur",
    "forward_port":80,
    "ssl_forced":true,
    "http2_support":true,
    "block_exploits":true,
    "allow_websocket_upgrade":true,
    "enabled":true
  }'

# Lister les proxy hosts existants
curl -s http://localhost:8282/api/nginx/proxy-hosts \
  -H "Authorization: Bearer $TOKEN" | python3 -m json.tool

# Forcer SSL sur un host existant (id récupéré du list)
curl -s -X PUT http://localhost:8282/api/nginx/proxy-hosts/{id} \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ssl_forced":true,"http2_support":true,...}'
```

### Config NPM stockée dans
```
/data/nginx/proxy_host/1.conf   ← travel-planner.camilleaubert.com
/data/nginx/proxy_host/2.conf   ← camilleaubert.com
/data/nginx/proxy_host/3.conf   ← nestor.camilleaubert.com
```

## Cloudflare DNS

Domaine : `camilleaubert.com`

Tous les enregistrements sont en mode **Proxied** (orange cloud).

| Nom | Type | Valeur |
|---|---|---|
| `@` (apex) | A | 13.39.194.192 |
| `www` | CNAME | camilleaubert.com |
| `nestor` | A | 13.39.194.192 |
| `travel-planner` | A | 13.39.194.192 |

## Firewall AWS Lightsail

⚠️ Les règles firewall **ne survivent pas** aux snapshots. Vérifier après chaque upgrade/migration.

Console AWS → Instance → Networking → IPv4 Firewall :

| Application | Protocole | Port | Source |
|---|---|---|---|
| SSH | TCP | 22 | 0.0.0.0/0 |
| HTTP | TCP | 80 | 0.0.0.0/0 |
| HTTPS | TCP | 443 | 0.0.0.0/0 |

## Dépannage

### HTTPS → 522, HTTP → 200
- Cause la plus fréquente : port 443 pas ouvert dans le firewall Lightsail
- Vérifier aussi que le proxy host NPM a bien `ssl_forced: true`

### DNS ne résout pas
- Propagation DNS Cloudflare : 1-5 minutes
- Vérifier : `dig nestor.camilleaubert.com` — doit retourner une IP Cloudflare (pas 13.39.194.192 directement)

### Plus assez de RAM
- `free -h` sur le serveur
- Si swap utilisé > 500 Mo → upgrader l'instance
- t3.nano (416 Mo) = **insuffisant** pour MySQL. Minimum t3.small (2 Go).

### Container "Restarting"
- `docker logs nestor-app --tail 30`
- Causes fréquentes : APP_KEY manquant, .env corrompu, DB inaccessible

## Commandes essentielles

```bash
# Connexion
ssh camille-prod

# Tous les conteneurs
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"

# Réseau
docker network inspect travel-network

# Logs
docker logs proxy-app-1 --tail 20
docker logs nestor-app --tail 30
docker logs nestor-db --tail 10

# Ressources
free -h
df -h /
uptime
```
