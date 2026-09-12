# Server Access

## Objective

Document how to access the production server for `camilleaubert.com` from a local development machine.

## Current setup (2026-09-12)

Two workstations are/were configured:

| Machine | Clé | Statut |
|---|---|---|
| Poste de travail principal (workspace CEGOS, `/var/www/html`) | `~/.ssh/camille-prod-lightsail` (Ed25519, dédiée) | ✅ **Primaire — validée et testée** |
| Ancien ordi perso (WSL) | `~/.ssh/lightsail-eu-west-3.pem` | ⚠️ À révoquer (toujours présente dans `authorized_keys`) |

La clé primaire a été générée sur le poste principal puis installée via le
client SSH navigateur de la console Lightsail (onglet *Connect* de l'instance) —
aucune dépendance à l'ancienne clé.

## SSH key location

The SSH private key must be stored locally outside the repository.

Primary key path (poste principal) :

```bash
~/.ssh/camille-prod-lightsail        # clé privée, chmod 600
~/.ssh/camille-prod-lightsail.pub    # clé publique
```

Permissions must be restricted:

```bash
chmod 600 ~/.ssh/camille-prod-lightsail
```

## SSH config

Local SSH config in:

```bash
~/.ssh/config
```

Host entry:

```sshconfig
Host camille-prod
  HostName 13.39.194.192
  User ubuntu
  IdentityFile ~/.ssh/camille-prod-lightsail
  IdentitiesOnly yes
```

## CLI access

Connect with:

```bash
ssh camille-prod
```

## VS Code access

Recommended extension:
- Remote - SSH

In VS Code:
1. Open command palette
2. Run `Remote-SSH: Connect to Host`
3. Select `camille-prod`

## Bootstrap d'une nouvelle clé (procédure utilisée le 2026-09-12)

Si la clé d'un poste est perdue, pas besoin de l'ancienne :

1. Générer une paire neuve en local :
   ```bash
   ssh-keygen -t ed25519 -f ~/.ssh/camille-prod-lightsail -N "" -C "camille-prod-$(hostname)-2026"
   ```
2. Console AWS → Lightsail → instance → **Connect** (SSH navigateur)
3. Dans le terminal navigateur :
   ```bash
   mkdir -p ~/.ssh && chmod 700 ~/.ssh
   echo '<clé publique>' >> ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   ```
4. Configurer `~/.ssh/config` (voir ci-dessus) et tester : `ssh camille-prod`

## Révocation de l'ancienne clé (à faire)

Sur le serveur (`ssh camille-prod`) :

```bash
# identifier la ligne de l'ancienne clé puis la supprimer
sed -i '/lightsail-eu-west-3/d' ~/.ssh/authorized_keys   # si commentée
# sinon : éditer ~/.ssh/authorized_keys et retirer la ligne correspondante
```

## Rules

- Never commit private SSH keys.
- Never store credentials in the repository.
- Keep machine-specific SSH configuration in `~/.ssh/config`.
- Document the convention in this repository.
