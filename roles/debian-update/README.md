Debian update role
==================

This role keeps Debian or Raspberry Pi OS hosts patched.  
It refreshes the APT cache, performs upgrades, removes obsolete packages, cleans the cache, and reboots only when the system requests it.

Requirements
------------

- Ansible 2.1+
- Debian-based target with APT available
- Privileged access (become) to apply upgrades and reboot

Role Variables
--------------

- `system_update_mode`  
  Controls the upgrade strategy supplied to `apt` (`safe`, `full`, `minimal`).  
  *Default*: `"safe"`

What the role does
------------------

- Refreshes the APT package index with a configurable cache validity.
- Runs `apt upgrade` with the requested mode (`safe` by default) to install updates.
- Removes unused packages via `apt autoremove`.
- Purges cached package files with `apt autoclean`.
- Checks for `/var/run/reboot-required` and triggers a reboot if the system needs it.

Example Playbook
----------------

```yaml
- hosts: debian
  become: true
  roles:
    - role: debian-update
      vars:
        system_update_mode: full
```

License
-------

MIT-0

Author Information
------------------

Created as part of a Raspberry Pi homelab Ansible setup.
Author: Enrico Cirignaco
