# Server Access

## Objective

Document how to access the production server for `camilleaubert.com` from a local development machine.

## Recommended local setup

Use:
- WSL
- OpenSSH
- VS Code
- Remote - SSH extension

## SSH key location

The SSH private key must be stored locally outside the repository.

Recommended path in WSL:

```bash
~/.ssh/lightsail-eu-west-3.pem
```

Permissions must be restricted:

```bash
chmod 600 ~/.ssh/lightsail-eu-west-3.pem
```

## SSH config

Recommended local SSH config in:

```bash
~/.ssh/config
```

Suggested host entry:

```sshconfig
Host camille-prod
  HostName 13.39.194.192
  User ubuntu
  IdentityFile ~/.ssh/lightsail-eu-west-3.pem
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

## Rules

- Never commit private SSH keys.
- Never store credentials in the repository.
- Keep machine-specific SSH configuration in `~/.ssh/config`.
- Document the convention in this repository.