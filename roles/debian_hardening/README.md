Debian hardening
================

This role applies a pragmatic Debian hardening baseline suitable for Raspberry Pi and general Debian servers.

Requirements
------------

- Ansible 2.1+
- `systemd` (used for unattended-upgrades weekly timer)

Role Variables
--------------

- SSH hardening
  - `ssh_password_auth` (bool, default: false)
  - `ssh_kbdinteractive_auth` (bool, default: false)
  - `ssh_pubkey_auth` (bool, default: true)
  - `ssh_permit_root_login` (string, default: "no")
  - `ssh_max_auth_tries` (int, default: 3)
  - `ssh_login_grace_time` (string, default: "30s")
  - `ssh_client_alive_interval` (int, default: 300)
  - `ssh_client_alive_count_max` (int, default: 2)
  - `ssh_allow_groups` (list, default: ["sudo"]) — rendered as space-separated groups

- Updates
  - `hardening_enable_weekly_upgrade` (bool, default: true)
    - true: install a systemd drop-in to run unattended-upgrades weekly
    - false: remove the drop-in and fall back to Debian’s default daily schedule
  - `hardening_disable_apt_daily_index_timer` (bool, default: true)
    - when true, disables/masks `apt-daily.timer` (daily APT index refresh)

- Services
  - `hardening_disable_services` (list, default: []) — units to disable/stop/mask

Files
-----

- `templates/hardening_ssh.conf.j2` — SSH drop-in generated from role variables, applied to `/etc/ssh/sshd_config.d/10-hardening.conf`
- `templates/20auto-upgrades.j2` — enables unattended-upgrades via APT periodic settings
- `files/security_unattended_upgrades.conf` — confines unattended-upgrades to security updates
- `files/apt-daily-upgrade.timer.override` — systemd timer drop-in to switch daily → weekly

What the role does
------------------

- SSH: ensures `Include /etc/ssh/sshd_config.d/*.conf` is present, deploys hardened settings from variables, validates with `sshd -t`, and restarts on change
- Updates: installs unattended-upgrades, enables APT periodic, limits to security updates, and optionally switches schedule to weekly
- Package index: optionally disables `apt-daily.timer` to reduce churn on low-end media
- Filesystem: enforces sane permissions for sensitive files; ensures sticky bit on `/tmp` and `/var/tmp`
- Kernel: applies a small set of safe sysctl hardening toggles
- Services: optionally disables, stops, and masks listed services

Example Playbook
----------------

```yaml
- hosts: managed
  become: true
  roles:
    - role: debian_hardening
      vars:
        # SSH policy
        ssh_allow_groups: ["sudo", "ansible"]

        # Updates
        hardening_enable_weekly_upgrade: true   # set to false to keep daily
        hardening_disable_apt_daily_index_timer: true

        # Optional service controls
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

Notes
-----

- The role ensures `/etc/ssh/sshd_config` includes the drop-in directory, adding an `Include /etc/ssh/sshd_config.d/*.conf` line if missing.
- `20auto-upgrades` is managed to guarantee unattended-upgrades is enabled; security-only scope is set via `51security-unattended-upgrades`.
- Weekly vs daily cadence is controlled by the presence of the systemd drop-in.
