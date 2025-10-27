Docker role
===========

This role installs Docker Engine on Debian-based hosts using the official Docker APT repository.  
It configures a sane default daemon profile, enables log rotation, and ensures the service stays running.

Requirements
------------

- Ansible 2.1+
- Debian or Raspberry Pi OS target with `apt` available
- Outbound HTTPS access to `download.docker.com` to retrieve the repository key and packages

Role Variables
--------------

This role does not expose user-facing variables yet. Override files or extend the role if custom daemon settings are required.

What the role does
------------------

- Installs prerequisite packages (`ca-certificates`, `curl`, `gnupg`) needed for APT repository setup.
- Creates `/etc/apt/keyrings` and downloads Docker’s GPG signing key (`docker.asc`).
- Adds the Docker Stable repository for the host’s architecture and Debian version codename.
- Installs Docker Engine components (`docker-ce`, CLI, containerd, buildx, compose plugin).
- Deploys `/etc/docker/daemon.json` with JSON-file logging (10 MB max size, 3 files) and sets the cgroup driver to `systemd`.
- Enables and starts the `docker` systemd service; restarts it when configuration changes occur.

Example Playbook
----------------

```yaml
- hosts: docker_hosts
  become: true
  roles:
    - role: docker
```

License
-------

MIT-0

Author Information
------------------

Created as part of a Raspberry Pi homelab Ansible setup.
Author: Enrico Cirignaco
