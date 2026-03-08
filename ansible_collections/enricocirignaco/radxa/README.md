# Ansible Collection - enricocirignaco.radxa

Radxa-focused Ansible collection for board provisioning and system configuration.

## Scope

This collection is intended for automation that is specific to Radxa devices and their operating system images.

## Current status

The collection structure is initialized and ready for roles, modules, and plugins.
As content is added, this README should be updated with:

- Included roles and plugins
- Supported boards and OS variants
- Required inventory variables
- Example playbooks

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
