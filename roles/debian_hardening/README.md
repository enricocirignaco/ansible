Debian hardening
================

This role applies a pragmatic Debian hardening baseline suitable for Raspberry Pi and general Debian servers.

Requirements
------------

- Ansible 2.1+
- `systemd` for timer overrides

Role Variables
--------------

Variables can be set in `group_vars` or directly in playbooks.

### SSH hardening
- `ssh_password_auth` (default: false)  
- `ssh_kbdinteractive_auth` (default: false)  
- `ssh_pubkey_auth` (default: true)  
- `ssh_permit_root_login` (default: "no")  
- `ssh_max_auth_tries` (default: 3)  
- `ssh_login_grace_time` (default: "30s")  
- `ssh_client_alive_interval` (default: 300)  
- `ssh_client_alive_count_max` (default: 2)  
- `ssh_allow_groups` (default: ["sudo"])

### Updates
- `hardening_disable_apt_daily_index_timer` (default: true)  
  Whether to disable the daily `apt-daily.timer` index refresh to reduce SD wear.

### Optional service controls
- `hardening_disable_services` (default: [])  
  List of services to disable/stop/mask (e.g. `["avahi-daemon", "cups"]`).

### Cron/at restrictions
- `hardening_cron_allow_users` (default: [])  
- `hardening_at_allow_users` (default: [])

Files
-----

- `files/hardening_ssh.conf`: SSH drop-in configuration applied to `/etc/ssh/sshd_config.d/10-hardening.conf`
- `files/security_unattended_upgrades.conf`: configures unattended-upgrades to install only security patches
- `files/apt-daily-upgrade.timer.override`: systemd drop-in to run upgrades weekly instead of daily

Example Playbook
----------------

```yaml
- hosts: managed
  become: true
  roles:
    - role: debian_hardening
      vars:
        ssh_allow_groups: ["sudo", "ansible"]
        hardening_disable_services:
          - avahi-daemon
```

License
-------

MIT-0

Author Information
------------------

Created as part of a Raspberry Pi homelab Ansible setup.
Author: Enrico Cirignaco
