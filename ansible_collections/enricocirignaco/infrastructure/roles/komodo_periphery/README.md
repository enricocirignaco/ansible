Komodo Periphery Role
======================

Deploys the Komodo Periphery agent as a single-container Docker Compose service.

What it does
-----------

- Validates required variables are present.
- Creates the deployment directory and periphery data directories with secure permissions.
- Renders `compose.yaml` from the role's `templates/compose.yaml.j2` and applies the stack with `community.docker.docker_compose_v2`.

Requirements
------------

- Target host must run Docker Engine with the Docker Compose plugin.
- Controller needs the `community.docker` collection installed (`ansible-galaxy collection install community.docker`).

Role Variables
--------------

Required (set via inventory or Ansible Vault):

- `komodo_periphery_deployment_directory`: absolute path for the Komodo Periphery deployment root (e.g. `/opt/komodo` or `/srv/komodo`).
- `komodo_periphery_version`: image tag/version to deploy for `ghcr.io/moghtech/komodo-periphery`.
- `komodo_periphery_core_address`: Periphery should use this Core address (full URL, e.g. `https://komodo.example.com`).
- `komodo_periphery_secret_core_public_key`: Core's public key string (store in vault and do not commit in plain text).

Optional:

- `komodo_periphery_secret_onboarding_key` (optional): onboarding key for automatic server creation; leave unset if not used.
- `komodo_periphery_connect_as` (default: inventory hostname): identity the Periphery reports to Core.
- `komodo_periphery_disable_terminals` (default: `false`): toggle to disable terminal features.
- `komodo_periphery_disable_container_exec` (default: `false`): toggle to disable container exec features.

Derived/Template variables
--------------------------

The role's `vars/main.yml` exposes the following Compose environment variables (rendered from the values above):

- `PERIPHERY_VERSION` — from `komodo_periphery_version`
- `PERIPHERY_PATH` and `PERIPHERY_KEYS_PATH` — derived from `komodo_periphery_deployment_directory`
- `PERIPHERY_CORE_PUBLIC_KEY`, `PERIPHERY_CORE_ADDRESS`, `PERIPHERY_CONNECT_AS`
- `PERIPHERY_ONBOARDING_KEY`, `PERIPHERY_DISABLE_TERMINALS`, `PERIPHERY_DISABLE_CONTAINER_EXEC`

Secret Management
-----------------

Store sensitive values in an encrypted vars file, for example `group_vars/komiphery/vault.yml`:

```yaml
komodo_periphery_deployment_directory: /opt/komodo
komodo_periphery_version: "1.10.0"
komodo_periphery_core_address: "https://komodo.example.com"

komodo_periphery_secret_core_public_key: !vault |
  $ANSIBLE_VAULT;1.1;AES256...
komodo_periphery_secret_onboarding_key: !vault |
  $ANSIBLE_VAULT;1.1;AES256...  # optional
```

Example Playbook
----------------

```yaml
- name: Deploy Komodo Periphery
  hosts: komodo_hosts
  become: true
  vars_files:
    - group_vars/komiphery/vault.yml
  roles:
    - role: enricocirignaco.infrastructure.komodo_periphery
```

Behavior
--------

The role renders `{{ komodo_periphery_deployment_directory }}/compose.yaml` (mode `0600`) and uses `community.docker.docker_compose_v2` to ensure the stack is running. Periphery state is persisted under `{{ komodo_periphery_deployment_directory }}/periphery` and keys under `{{ komodo_periphery_deployment_directory }}/periphery_keys`.

License
-------

MIT-0 (see repository root for details).

Author Information
------------------

enricocirignaco
