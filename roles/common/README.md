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
