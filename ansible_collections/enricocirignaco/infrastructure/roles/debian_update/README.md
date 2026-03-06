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

- `debian_update_mode`  
  Controls the upgrade strategy supplied to `apt` (`safe`, `full`, `minimal`).  
  *Default*: `"safe"`

- `debian_update_auto_reboot`  
  Allows the role to reboot automatically when `/var/run/reboot-required` exists.  
  *Default*: `false`

What the role does
------------------

- Refreshes the APT package index with a configurable cache validity.
- Runs `apt upgrade` with the requested mode (`safe` by default) to install updates.
- Removes unused packages via `apt autoremove`.
- Purges cached package files with `apt autoclean`.
- Checks for `/var/run/reboot-required` and logs whether a reboot is required.
- Reboots only if a reboot is required and `debian_update_auto_reboot` is `true`.

Example Playbook
----------------

```yaml
- hosts: debian
  become: true
  roles:
    - role: debian_update
      vars:
        debian_update_mode: full
        debian_update_auto_reboot: true
```

License
-------

MIT-0

Author Information
------------------

Created as part of a Raspberry Pi homelab Ansible setup.
Author: Enrico Cirignaco
