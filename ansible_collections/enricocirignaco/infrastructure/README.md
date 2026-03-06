# Ansible Collection - enricocirignaco.infrastructure

Infrastructure automation collection for Debian and Raspberry Pi hosts.

## Included roles

- `debian_bootstrap`: one-time bootstrap of admin access and base SSH configuration.
- `common`: baseline host configuration (packages, locale, timezone, users).
- `debian_hardening`: practical hardening defaults for SSH, updates, and sysctl.
- `debian-update`: host patching and controlled reboot flow.
- `network`: NetworkManager-based interface configuration and discovery behavior.
- `raspberry`: Raspberry Pi hardware and boot-level baseline.
- `docker`: Docker Engine installation and daemon tuning.
- `komodo`: Komodo stack deployment with Docker Compose.
- `backup`: SMB-based backup mount setup and backup helpers.

## Requirements

- `ansible-core >= 2.14`
- Dependent collections are declared in `galaxy.yml` and installed automatically when using `ansible-galaxy collection install`.

## Build

```bash
ansible-galaxy collection build ansible_collections/enricocirignaco/infrastructure
```

## Install locally

```bash
ansible-galaxy collection install ./enricocirignaco-infrastructure-1.0.1.tar.gz
```
