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
