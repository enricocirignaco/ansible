Role Name
=========

Configure NetworkManager connections (IPv4 static or DHCP, IPv6 policy, discovery).

Requirements
------------

- NetworkManager on the target host (the role will install it on Debian-based systems).
- Ansible 2.9+ recommended.
- Collections:
  - community.general (provides nmcli)
  - ansible.utils (provides ipaddr test/filter)

Role Variables
--------------

Mandatory variables (must be provided via inventory / host_vars / group_vars):

- network_interface (string) — interface name, e.g., "eth0"
- network_method_v4 (string, default: "static") — IPv4 method ("static" or "dhcp")

Required when network_method_v4 = "static":

- network_address (CIDR) — IPv4 with prefix, e.g., "192.168.1.157/24"
- network_gateway (ip) — IPv4 gateway, e.g., "192.168.1.1"
- network_dns (list of ip) — DNS servers, e.g., ["192.168.1.1", "1.1.1.1"]

Optional variables:

- network_type (string, default: "ethernet") — connection type ("ethernet" or "wifi")
- network_disable_ipv6 (bool, default: true) — disable IPv6 on the connection via nmcli

Files
-----

- tasks/main.yml — implements the role tasks (installs NetworkManager, configures connection via nmcli, optional IPv6 and changes)
- meta/main.yml — declares collection dependencies (community.general, ansible.utils)

What the role does
------------------

- Installs network-manager package on Debian-based systems and ensures the NetworkManager service is enabled and running.
- Configures an IPv4 connection using community.general.nmcli (static or DHCP) with provided address, gateway, DNS, type, and connection name.
- Optionally sets ipv6_method=ignore on the connection when network_disable_ipv6 is true.
- Manages discovery services: can enable avahi-daemon and set LLMNR=no in systemd-resolved configuration.
- Registers task results (nmcli changes, IPv6 change, service changes) so you can decide whether to trigger a reboot (controlled by network_reboot_if_changed).

Example Playbook
----------------

```yaml
- hosts: all
  become: true
  roles:
    - role: network
      vars:
        network_interface: eth0
        network_method_v4: static
        network_address: 192.168.1.157/24
        network_gateway: 192.168.1.1
        network_dns:
          - 192.168.1.1
          - 1.1.1.1
```

DHCP example:

```yaml
- hosts: all
  become: true
  roles:
    - role: network
      vars:
        network_interface: eth0
        network_method_v4: dhcp
```

License
-------

MIT-0

Author Information
------------------

Author: Enrico Cirignaco

Notes
-----

- The role registers nmcli and other task results and exposes .changed flags so playbooks can make safe decisions about restarting or rebooting.
- Network changes do not always require a reboot. nmcli reports whether it changed state; use network_reboot_if_changed to opt in to reboots only when changes occurred.
- Ensure the required collections are available (via requirements.yml or galaxy install) before running the role on a control node that enforces collection usage.
