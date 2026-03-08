# enricocirignaco.radxa.rock_4b

Hardening and cleanup baseline for Radxa Rock 4B hosts.

## What the role does

- Validates host platform (Debian-family, Rock 4B model check by default)
- Removes KDE plus desktop-related packages when enabled
- Stops/disables common display manager services
- Removes default users (`radxa`, `rock`) when enabled
- Disables HDMI by appending `video=HDMI-A-1:d` to boot `extraargs`
- Reboots when required changes are applied

## Requirements

- `ansible-core >= 2.14`
- Debian-family target host

## Role variables

User-tunable defaults are in `defaults/main.yml`. Internal constants are in `vars/main.yml`.

- `rock_4b_remove_kde_desktop` (bool, default: `true`)
- `rock_4b_remove_radxa_user` (bool, default: `true`)
- `rock_4b_remove_rock_user` (bool, default: `true`)
- `rock_4b_disable_hdmi` (bool, default: `true`)
- `rock_4b_reboot_if_required` (bool, default: `true`)
- `rock_4b_extra_desktop_packages` (additional package names to purge)

## Example playbook

```yaml
- hosts: rock4b
  become: true
  roles:
    - role: enricocirignaco.radxa.rock_4b
      vars:
        rock_4b_remove_kde_desktop: true
        rock_4b_extra_desktop_packages:
          - firefox-esr
```
