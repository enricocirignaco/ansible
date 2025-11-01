Common Role
===========

This role provides a baseline configuration for all managed Debian-based hosts in the homelab.  
It ensures system identity, base packages, safe upgrades and essential system services.

Requirements
------------

- Ansible 2.1+
- Collections:
  - `community.general` (for locale management)

Role Variables
--------------

Variables can be set in `defaults/main.yml`, overridden in `group_vars/` or `host_vars/`.

- `common_base_packages`: List of baseline packages to install.  
  *Default*: `['nano', 'curl', 'htop', 'git', 'sudo']`

- `timezone`: Timezone string to apply.  
  *Default*: `Europe/Zurich`

- `hostname`: Hostname to set 
  *Default*: `{{ inventory_hostname }}`

- `common_admin_user`: Name of the privileged service account the role manages.
  *Default*: `admin`
- `common_admin_shell`: Login shell for the admin user. *Default*: `/bin/bash`
- `common_admin_home`: Home directory path for the admin user. *Default*: `/home/{{ common_admin_user }}`
- `common_admin_groups`: List of supplementary groups (e.g. `sudo`) the admin user should belong to.
- `common_admin_authorized_keys`: List of SSH public keys to install into the admin user’s `authorized_keys`. Must contain at least one entry.

Dependencies
------------

None. This role is self-contained, but assumes an admin user exists (usually created during bootstrap).

Example Playbook
----------------

```yaml
- hosts: managed
  become: true
  roles:
    - role: common
      vars:
        hostname: srvprod01
```

License
-------

MIT-0

Author Information
------------------

Created as part of a Raspberry Pi homelab Ansible setup.
Author: Enrico Cirignaco
