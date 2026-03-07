Common Role
===========

This role provides a baseline configuration for all managed Debian-based hosts in the homelab.  
It configures host identity, admin access, locales, essential services, optional Tailscale, and optional custom CA trust.

Requirements
------------

- Ansible 2.14+
- Collections:
  - `community.general` (for locale management)

Role Variables
--------------

Variables can be set in `defaults/main.yml`, overridden in `group_vars/` or `host_vars/`.

- `common_timezone`  
  Timezone string to apply.  
  *Default*: `Europe/Zurich`

- `common_hostname`  
  Hostname to set on the target host.  
  *Default*: `{{ hostname | default(inventory_hostname) }}`

- `common_base_packages`  
  List of baseline packages to install.  
  *Default*: `['nano', 'curl', 'htop', 'git', 'sudo', 'locales', 'ca-certificates']`

- `common_admin_user`  
  Name of the privileged account managed by the role.  
  *Default*: `admin`

- `common_admin_shell`  
  Login shell for `common_admin_user`.  
  *Default*: `/bin/bash`

- `common_admin_home`  
  Home directory for `common_admin_user`.  
  *Default*: `/home/{{ common_admin_user }}`

- `common_admin_groups`  
  Supplementary groups assigned to `common_admin_user`.  
  *Default*: `['sudo']`

- `common_admin_authorized_keys`  
  SSH public keys installed for `common_admin_user`. Must contain at least one key (the role asserts this).  
  *Default*: `[]` (must be overridden)

- `common_enable_cron`  
  When `true`, ensures the `cron` package is installed and the service is enabled/running.  
  *Default*: `true`

- `common_install_tailscale`  
  When `true`, configures the official Tailscale apt repository, installs `tailscale`, and enables `tailscaled`.  
  *Default*: `false`

- `common_install_homelab_certs`  
  When `true`, installs `files/root.crt` into the local trust store and runs `update-ca-certificates` on change.  
  *Default*: `false`

- `common_system_locale`  
  Locale value used for `LC_CTYPE` and locale generation.  
  *Default*: `de_CH.UTF-8`

- `common_system_language`  
  Language value used for `LANG` and locale generation.  
  *Default*: `en_US.UTF-8`

Dependencies
------------

None. This role is self-contained, but assumes an admin user exists (usually created during bootstrap).

Example Playbook
----------------

```yaml
- hosts: managed
  become: true
  roles:
    - role: enricocirignaco.infrastructure.common
      vars:
        common_hostname: srvprod01
```

License
-------

MIT-0

Author Information
------------------

Created as part of a Raspberry Pi homelab Ansible setup.
Author: Enrico Cirignaco
