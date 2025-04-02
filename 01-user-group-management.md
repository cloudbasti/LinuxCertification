## User Management
- Switch user: `sudo su - john`
- User information: `/etc/passwd`
- User limits: `/etc/security/limits.conf` or `/etc/security/limits.d/`

### Viewing User and Group Information
- `cat /etc/passwd` - list all users
- `cat /etc/group` - list all groups
- `getent passwd` / `getent group` - list all users/groups from all sources
- `groups` / `groups username` - list current user's groups / specific user's groups
- `getent passwd username` / `getent group groupname` - display specific user/group info
- `last` / `who` / `w` - show login history / who is logged in / what they're doing
- `id` - show user and group IDs

### User and Group Administration
- `useradd username` / `groupadd groupname` - create user/group
- `userdel username` / `groupdel groupname` - delete user/group
- `usermod` / `groupmod` - modify user/group
- `passwd username` - set/change user password
- `gpasswd groupname` - set/change group password
- `chage username` - change user password expiry information
  
### For User Authentication and Sudo
- `/etc/sudoers` and `/etc/sudoers.d/` - sudo configuration files
- `visudo` - safely edit the sudoers file
- `sudo -l` - list user's sudo privileges
- Basic sudoers entry: `username ALL=(ALL) ALL`
- Passwordless sudo: `username ALL=(ALL) NOPASSWD: ALL`
- Command-specific sudo: `username ALL=(ALL) /bin/systemctl restart apache2`
- Group sudo access: `%admin ALL=(ALL) ALL`

### For User Account Management
- `chfn username` - change user's full name information
- `chsh -s /bin/bash username` - change user's login shell
- `passwd -l username` / `passwd -u username` - lock/unlock user account
- `passwd -e username` - expire password (force user to change at next login)
- `passwd -d username` - delete password (make passwordless)
- `pwck` - verify integrity of password files
- `grpck` - verify integrity of group files

### For User Expiration
- `usermod -e YYYY-MM-DD username` - set account expiration date
- `chage -E YYYY-MM-DD username` - alternative to set expiration
- `chage -M 90 username` - set maximum password age (days)
- `chage -m 7 username` - set minimum password age (days)
- `chage -W 14 username` - set password expiration warning days
- `chage -I 30 username` - set account inactivity lock days

### For User Resource Limits
- Basic `/etc/security/limits.conf` examples:
  ```
  username hard nproc 50      # Hard limit of 50 processes
  username soft nproc 30      # Soft limit of 30 processes
  username hard nofile 4096   # Hard limit of 4096 open files
  @developers soft memlock 64000  # Soft memlock limit for group
  ```
- `ulimit -a` - show all limits for current shell
- `ulimit -n 2048` - set file descriptor limit for shell

### For PAM Configuration
- `/etc/pam.d/` - PAM configuration files
- `/etc/pam.d/common-auth` - authentication configuration
- `/etc/pam.d/common-password` - password policies
- `/etc/login.defs` - shadow password suite configuration

### For ACL Management
- `getfacl file` - show file ACLs
- `setfacl -m u:username:rwx file` - add user ACL
- `setfacl -m g:groupname:rx file` - add group ACL
- `setfacl -x u:username file` - remove specific user ACL
- `setfacl -b file` - remove all ACLs
- `setfacl -d -m u:username:rwx directory` - set default ACLs for new files

### For User Templates
- `/etc/skel/` - template directory for new user home directories
- `useradd -m -k /custom/skel username` - create user with custom skel directory

### User Management Scripts
- `newusers` - create multiple users from a file
- `pwconv` / `pwunconv` - convert to/from shadow passwords
- `grpconv` / `grpunconv` - convert to/from shadow groups

- ## Environment Configuration
- `source ~/.bashrc` / `source /etc/profile` - reload user/system bash configuration
- `/etc/profile.d/` - directory containing system-wide shell scripts executed at login
- `env` - display all environment variables
- `/etc/environment` - system-wide environment variable file
- `echo $PATH` - display the current executable search path
- `export PATH=$PATH:/new/path` - add directory to PATH temporarily
- `export VARIABLE=value` - set environment variable for current session

### For SELinux User Type Management

- `semanage login -l` - List all SELinux login mappings (Linux user to SELinux user)
- `semanage user -l` - List all SELinux users and their MLS/MCS ranges
- `id -Z` - Display current user's SELinux context
- `ps -Z` - Show SELinux contexts for running processes
- `ls -Z` - Show SELinux contexts for files and directories

### Creating and Modifying SELinux Users

- `semanage user -a -R "staff_r sysadm_r" -P user myseuser` - Create new SELinux user with specified roles
- `semanage user -m -R "staff_r sysadm_r system_r" myseuser` - Modify roles for existing SELinux user
- `semanage user -d myseuser` - Delete a SELinux user

### Mapping Linux Users to SELinux Users

- `semanage login -a -s user_u username` - Map Linux user to SELinux user
- `semanage login -m -s staff_u username` - Change SELinux user mapping for Linux user
- `semanage login -d username` - Remove SELinux mapping for Linux user
- `semanage login -a -s user_u -r s0:c100-c109 username` - Map Linux user with specific MLS/MCS range

### Managing SELinux Defaults

- `semanage login -a -s user_u %groupname` - Map all users in Linux group to SELinux user
- `semanage login -m -S targeted -s unconfined_u __default__` - Set default SELinux user for new Linux users

### System Configuration for SELinux Users

- `/etc/selinux/targeted/seusers` - Direct mapping file (modified through semanage)
- `/etc/selinux/targeted/contexts/users/*` - SELinux user definition files
- `chcon -u system_u -r system_r -t etc_t /path/to/file` - Change SELinux context for a file
- `restorecon -v /path/to/file` - Restore default SELinux context to file

### For SELinux User Troubleshooting

- `sestatus` - Show SELinux status and policy
- `getsebool -a | grep user` - List SELinux booleans related to users
- `setsebool -P allow_guest_exec_content on` - Permanently set a SELinux boolean
- `ausearch -m avc --start recent` - Search audit logs for recent SELinux denials
- `aureport -a` - Generate summary report of SELinux audit events

These commands cover the essential SELinux user management operations that might appear in a Linux administration exam. Would you like me to explain any of these commands in more detail?
