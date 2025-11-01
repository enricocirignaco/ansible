Raspberry role
==============

This role applies a conservative hardware baseline for Raspberry Pi hosts.  
It hardens the boot configuration, enables the hardware watchdog, expands the root filesystem once, and reboots only when required.

Requirements
------------

- Ansible 2.1+
- Target host must expose the Raspberry Pi boot configuration (`config.txt`)
- `raspi-config` available on the target when filesystem expansion is desired

Role Variables
--------------

- `raspberry_boot_config_path`  
  Path to the Raspberry Pi `config.txt` file that should be managed.  
  *Default*: `/boot/firmware/config.txt`
- `raspberry_disable_pi_user`  
  When `true`, lock the legacy `pi` account and set its shell to `/usr/sbin/nologin`.  
  *Default*: `false`
- `raspberry_expand_rootfs`  
  Controls whether `raspi-config --expand-rootfs` should run to grow the root filesystem.  
  *Default*: `false`

What the role does
------------------

- Ensures a boot configuration block that disables Wi-Fi, Bluetooth, HDMI output, onboard audio, and camera auto-detection.
- Reduces GPU memory allocation to 16 MB to reserve RAM for the CPU.
- Enables the hardware watchdog through both firmware (`dtparam=watchdog=on`) and the systemd service.
- Invokes `raspi-config --expand-rootfs` to grow the root filesystem to the full media size (ignored if the filesystem is already expanded).
- Reboots the host when the boot configuration or filesystem expansion reports a change.

Example Playbook
----------------

```yaml
- hosts: raspberry
  become: true
  roles:
    - role: raspberry
      vars:
        raspberry_boot_config_path: /boot/config.txt
```

License
-------

MIT-0

Author Information
------------------

Created as part of a Raspberry Pi homelab Ansible setup.
Author: Enrico Cirignaco
