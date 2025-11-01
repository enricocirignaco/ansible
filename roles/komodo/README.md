Komodo Role
===========

Deploys the Komodo Core, FerretDB, and Postgres stack using Docker Compose. The role:

- validates that all required version pins, host settings, and secrets are provided
- creates the deployment, data, and periphery directories
- installs a static `docker-compose.yaml` (and optional override) from the role’s `files/`
- renders a `.env` file containing all runtime variables with restrictive permissions
- applies the stack with `community.docker.docker_compose_v2`

Requirements
------------

- Target host runs Docker Engine with the Docker Compose plugin.
- Python on the target host must include the Docker SDK (installed automatically with the `community.docker` collection when using `pip` extras, or via package manager).
- Controller has the Ansible `community.docker` collection available (`ansible-galaxy collection install community.docker`).

Role Variables
--------------

### Required

Provide these (preferably via inventory or Ansible Vault):

- `komodo_deployment_directory`: absolute path for the deployment root, e.g. `/opt/komodo`.
- `komodo_postgres_version`, `komodo_ferretdb_version`, `komodo_core_version`, `komodo_periphery_version`: image tags to deploy.
- `komodo_host_url`: external URL used by Komodo for OAuth/webhook links.
- `komodo_secret_db_username`, `komodo_secret_db_password`: credentials shared by Postgres, FerretDB, and Komodo Core.
- `komodo_secret_passkey`: shared passkey between Core and Periphery.
- `komodo_secret_webhook_secret`: secret for validating inbound webhooks.
- `komodo_secret_jwt_secret`: signing key for Komodo JWT tokens.

The role asserts that each value is defined and non-empty before proceeding.

### Optional

- `komodo_timezone` (default `"Europe/Zurich"`): injected into all services for scheduling and logging.
- `komodo_first_server` (default `"https://periphery:8120"`): initial periphery endpoint Komodo offers during onboarding.
- `komodo_jwt_ttl` (default `"1-day"`): JWT token lifetime.
- `komodo_local_auth` (default `true`): enables Komodo’s local username/password login.
- `periphery_ssl_enabled` (default `true`): enables TLS for the periphery container.

Derived compose variables such as `KOMODO_BACKUPS_PATH`, `KOMODO_SYNCS_PATH`, and `PERIPHERY_PATH` are computed relative to `komodo_deployment_directory`.

Secret Management
-----------------

Store sensitive values in an encrypted vars file, for example `group_vars/komodo/vault.yml`:

```yaml
komodo_deployment_directory: /opt/komodo
komodo_postgres_version: "16-alpine"
komodo_ferretdb_version: "1.21.0"
komodo_core_version: "1.10.0"
komodo_periphery_version: "1.10.0"
komodo_host_url: "https://komodo.example.com"

komodo_secret_db_username: admin
komodo_secret_db_password: !vault |
  $ANSIBLE_VAULT;1.1;AES256...
komodo_secret_passkey: !vault |
  $ANSIBLE_VAULT;1.1;AES256...
komodo_secret_webhook_secret: !vault |
  $ANSIBLE_VAULT;1.1;AES256...
komodo_secret_jwt_secret: !vault |
  $ANSIBLE_VAULT;1.1;AES256...
```

Example Playbook
----------------

```yaml
- name: Deploy Komodo
  hosts: komodo
  become: true
  vars_files:
    - group_vars/komodo/vault.yml
  roles:
    - role: komodo
```

The role copies `docker-compose.yaml`, renders `{{ komodo_deployment_directory }}/.env` with mode `0600`, and ensures the stack is running. Backup archives and sync files are placed under `{{ komodo_deployment_directory }}/backups` and `{{ komodo_deployment_directory }}/syncs`; periphery state persists under `{{ komodo_deployment_directory }}/periphery`.

License
-------

MIT-0 (see repository root for details).
