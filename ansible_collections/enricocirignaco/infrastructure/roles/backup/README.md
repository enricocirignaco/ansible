Backup Role
===========

Mounts an SMB/CIFS backup share on managed hosts and prepares credentials with restrictive permissions.
The role also contains optional snapshot automation variables for future extension.

Requirements
------------

- Debian-based host with `apt`.
- Network access to the SMB server.
- Valid SMB credentials.
- Collection dependency: `ansible.posix`.

Role Variables
--------------

- `backup_smb_share` (required): SMB share in `host/share` format.
- `backup_smb_username` (required): Username used to authenticate to SMB.
- `backup_smb_password` (required): Password used to authenticate to SMB.
- `backup_smb_mountpoint` (default: `/mnt/backup`): local mount path.
- `backup_smb_creds_file` (default: `/root/.smb/credentials`): credentials file path.
- `backup_smb_mount_opts`: mount options passed to `ansible.posix.mount`.
- Snapshot-related variables exist in `defaults/main.yml` for optional script-based backup automation.

Dependencies
------------

- None (role-level).
- Uses `ansible.posix.mount`.

Example Playbook
----------------

```yaml
- hosts: backups
  become: true
  roles:
    - role: backup
      vars:
        backup_smb_share: "nas.local/backups"
        backup_smb_username: "backupuser"
        backup_smb_password: "{{ vault_backup_smb_password }}"
        backup_smb_mount_opts: "credentials=/root/.smb/credentials,uid=1000,gid=1000,file_mode=0600,dir_mode=0700,vers=3.0,_netdev"
```

License
-------

MIT-0

Author Information
------------------

Enrico Cirignaco
