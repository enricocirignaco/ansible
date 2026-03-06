Debian Bootstrap Role
=====================

Version
-------

Current role version: `1.0.1`  
Canonical source: `roles/debian_bootstrap/VERSION`

Description
-----------

Bootstraps Debian-based hosts for ongoing Ansible management by:

- ensuring Python 3 is present
- creating a dedicated admin account
- installing one or more SSH public keys
- optionally enabling passwordless sudo via `/etc/sudoers.d`
- verifying SSH access as the new admin user

Operational Guidance
--------------------

This role is written to be idempotent, but it is intended to be run **once** during host bootstrap, in a **standalone playbook**.

Reason: the role resets the SSH connection and validates login as the new admin user, which is appropriate for first-time provisioning but can introduce side effects in larger multi-role plays.

Requirements
------------

- Debian-based target host
- privilege escalation (`become: true`)
- initial SSH user with rights to create users and manage sudoers

Role Variables
--------------

Defaults are defined in `defaults/main.yml`:

- `debian_bootstrap_ansible_user`  
  Admin account to create and validate.  
  Default: `ansible`

- `debian_bootstrap_ansible_groups`  
  Groups assigned to the admin account.  
  Default: `["sudo"]`

- `debian_bootstrap_enable_nopasswd_sudo`  
  Whether to create passwordless sudo drop-in for the admin account.  
  Default: `true`  
  Behavior: when `false`, the drop-in file is removed.

- `debian_bootstrap_admin_pubkeys`  
  List of SSH public keys to install for the admin account.  
  Default: empty list (must be overridden).

Example Standalone Playbook
---------------------------

```yaml
---
- name: Bootstrap Debian hosts
  hosts: bootstrap
  become: true
  gather_facts: false
  roles:
    - role: debian_bootstrap
      vars:
        debian_bootstrap_ansible_user: ansible
        debian_bootstrap_ansible_groups: ["sudo"]
        debian_bootstrap_enable_nopasswd_sudo: true
        debian_bootstrap_admin_pubkeys:
          - "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... your-key-comment"
```

License
-------

MIT-0
