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
