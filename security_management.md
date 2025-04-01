## Security Management

### User Security
- `passwd -l username` - lock user account
- `passwd -u username` - unlock user account
- `chage -l username` - list password aging info
- `chage -M 60 username` - set max password age
- `chage -d 0 username` - force password change

### File Permissions
- `chmod 600 file` - owner read/write only
- `chmod 750 dir` - rwx for owner, rx for group, nothing for others
- `chmod u+x file` - add execute permission for owner
- `chmod -R g+w dir` - add write permission for group recursively
- `chmod a-x file` - remove execute permission for all users
- `chown user:group file` - change owner and group
- `chattr +i file` - make file immutable
- `lsattr file` - list file attributes

### SELinux/AppArmor
- `getenforce` - show SELinux state
- `setenforce 0` - set SELinux to permissive mode
- `setenforce 1` - set SELinux to enforcing mode
- `ls -Z file` - show SELinux context
- `chcon -t type file` - change SELinux context type
- `aa-status` - show AppArmor status
- `apparmor_parser -r /etc/apparmor.d/profile` - reload profile

### Service Security
- `systemctl mask service` - prevent service from being started
- `systemctl unmask service` - remove prevention
- `systemctl list-dependencies service` - show dependencies
