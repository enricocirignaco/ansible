Backup Role
===========

Mounts an SMB/CIFS backup share on managed hosts and prepares credentials with restrictive permissions.
The role also includes optional daily rpi-clone automation (install, runner script, cron schedule, and ntfy notification).

Requirements
------------

- Debian-based host with `apt`.
- Network access to the SMB server.
- Valid SMB credentials.
- Collection dependency: `ansible.posix`.

Role Variables
--------------

Required Variables
------------------

- `backup_smb_share`: SMB share in `host/share` format.
- `backup_smb_username`: username used to authenticate to SMB.
- `backup_smb_password`: password used to authenticate to SMB.
- `backup_smb_mountpoint`: local mount path. Default is empty, so set it explicitly.

Optional Variables
------------------

- `backup_smb_creds_file` (default: `/root/.smb/credentials`): credentials file path.
- `backup_smb_uid` (default: `1000`): uid used in mount options.
- `backup_smb_gid` (default: `1000`): gid used in mount options.
- `backup_smb_mount_opts` (default from vars): mount options passed to `ansible.posix.mount`.

- `backup_rpi_clone_enabled` (default: `false`): enables rpi-clone automation tasks.
- `backup_rpi_clone_path` (default: `/usr/local/sbin/rpi-clone`): installed binary path.
- `backup_rpi_clone_args` (default: empty): arguments passed to rpi-clone.
- `backup_rpi_clone_cron_script_path` (default: `/usr/local/sbin/rpi_clone_cron.sh`): generated runner script path.
- `backup_rpi_clone_cron_hour` (default: `00`): hour when daily rpi-clone job runs.
- `backup_rpi_clone_cron_minute` (default: `00`): minute when daily rpi-clone job runs.
- `backup_rpi_clone_log_file` (default: `/var/log/rpi-clone_cron.log`): log file path.
- `backup_rpi_clone_ntfy_url` (default: empty): ntfy endpoint used for success/error notifications.

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
        backup_smb_mountpoint: "/mnt/backup"
        backup_smb_mount_opts: "credentials=/root/.smb/credentials,uid=1000,gid=1000,file_mode=0600,dir_mode=0700,vers=3.0,_netdev"
        backup_rpi_clone_enabled: true
        backup_rpi_clone_args: "-u -m /boot/firmware mmcblk0"
        backup_rpi_clone_cron_hour: "2"
        backup_rpi_clone_cron_minute: "15"
        backup_rpi_clone_log_file: "/var/log/rpi-clone_cron.log"
        backup_rpi_clone_ntfy_url: "https://ntfy.example.com/rpi-clone"
```

License
-------

MIT-0

Author Information
------------------

Enrico Cirignaco
