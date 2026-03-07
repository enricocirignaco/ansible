Debian network role
===================

Configure NetworkManager connections (IPv4 static or DHCP, IPv6 policy, discovery).

Requirements
------------

- NetworkManager on the target host (the role will install it on Debian-based systems).
- Ansible 2.14+.
- Collections:
  - community.general (provides nmcli)
  - ansible.utils (provides ipaddr test/filter)

Role Variables
--------------

Mandatory variables (must be provided via inventory / host_vars / group_vars):

- debian_network_interface (string) — interface name, e.g., "eth0"
- debian_network_method_v4 (string, default: "dhcp") — IPv4 method ("static" or "dhcp")

Required when debian_network_method_v4 = "static":

- debian_network_address (CIDR) — IPv4 with prefix, e.g., "192.168.1.157/24"
- debian_network_gateway (ip) — IPv4 gateway, e.g., "192.168.1.1"
- debian_network_dns (list of ip) — DNS servers, e.g., ["192.168.1.1", "1.1.1.1"]

Optional variables:

- debian_network_type (string, default: "ethernet") — connection type ("ethernet" or "wifi")
- debian_network_disable_ipv6 (bool, default: true) — disable IPv6 on the connection via nmcli
- debian_network_enable_avahi (bool, default: true) — enable/disable avahi-daemon (mDNS)
- debian_network_reboot_if_changed (bool, default: true) — reboot after network changes are applied
- debian_network_wifi_ssid (string, default: "") — required when debian_network_type is "wifi"
- debian_network_wifi_psk (string, default: "") — required when debian_network_type is "wifi" (store in Ansible Vault)

Files
-----

- tasks/main.yml — implements the role tasks (installs NetworkManager, configures connection via nmcli, optional IPv6 and changes)
- meta/main.yml — declares collection dependencies (community.general, ansible.utils)

What the role does
------------------

- Installs network-manager package on Debian-based systems and ensures the NetworkManager service is enabled and running.
- Configures an IPv4 connection using community.general.nmcli (static or DHCP) with provided address, gateway, DNS, type, and connection name.
- Optionally sets ipv6_method=ignore on the connection when debian_network_disable_ipv6 is true.
- Manages discovery services: enables or disables avahi-daemon based on debian_network_enable_avahi.
- Registers task results (nmcli changes, IPv6 change, service changes) so you can decide whether to trigger a reboot (controlled by debian_network_reboot_if_changed).

Example Playbook
----------------

```yaml
- hosts: all
  become: true
  roles:
    - role: debian_network
      vars:
        debian_network_interface: eth0
        debian_network_method_v4: static
        debian_network_address: 192.168.1.157/24
        debian_network_gateway: 192.168.1.1
        debian_network_dns:
          - 192.168.1.1
          - 1.1.1.1
```

DHCP example:

```yaml
- hosts: all
  become: true
  roles:
    - role: debian_network
      vars:
        debian_network_interface: eth0
        debian_network_method_v4: dhcp
```

Wi-Fi example:

```yaml
- hosts: all
  become: true
  roles:
    - role: debian_network
      vars:
        debian_network_interface: wlan0
        debian_network_type: wifi
        debian_network_method_v4: dhcp
        debian_network_wifi_ssid: "MySSID"
        debian_network_wifi_psk: "{{ vault_wifi_psk }}"
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
- Network changes do not always require a reboot. nmcli reports whether it changed state; use debian_network_reboot_if_changed to opt in to reboots only when changes occurred.
- Ensure the required collections are available (via requirements.yml or galaxy install) before running the role on a control node that enforces collection usage.
