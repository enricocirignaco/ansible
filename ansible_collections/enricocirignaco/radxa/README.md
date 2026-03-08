# Ansible Collection - enricocirignaco.radxa

Radxa-focused Ansible collection for board provisioning and system configuration.

## Scope

This collection is intended for automation that is specific to Radxa devices and their operating system images.

## Included roles

- `rock_4b`: baseline cleanup and hardening for Radxa ROCK Pi 4B hosts.

## Requirements

- `ansible-core >= 2.14`

## Build

```bash
ansible-galaxy collection build ansible_collections/enricocirignaco/radxa
```

## Install locally

```bash
ansible-galaxy collection install ./enricocirignaco-radxa-1.0.0.tar.gz
```
