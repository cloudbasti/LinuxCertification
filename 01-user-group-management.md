##  User Management

### Overview and Verification
```bash
cat /etc/passwd                # List all users
cat /etc/group                 # List all groups
getent passwd                  # List all users from all sources
getent group                   # List all groups from all sources
groups                         # List current user's groups
groups username                # List specific user's groups
getent passwd username         # Display specific user info
getent group groupname         # Display specific group info
id                             # Show user and group IDs
id -Z                          # Display current user's SELinux context
last / who / w                 # Show login history / who's logged in / what they're doing
sudo -l                        # List user's sudo privileges
env                            # Display all environment variables
echo $PATH                     # Display the current executable search path
```

### Create and Manage Users/Groups
```bash
# Basic user/group operations
useradd username               # Create user
useradd -m -k /custom/skel username  # Create user with custom skel directory
userdel username               # Delete user
usermod -options username      # Modify user
passwd username                # Set/change user password
groupadd groupname             # Create group
groupdel groupname             # Delete group
groupmod -options groupname    # Modify group
gpasswd groupname              # Set/change group password
sudo su - john                 # Switch to user john
visudo                         # Safely edit the sudoers file

# Account management
chfn username                  # Change user's full name information
chsh -s /bin/bash username     # Change user's login shell
passwd -l username             # Lock user account
passwd -u username             # Unlock user account
passwd -e username             # Expire password (force user to change at next login)
passwd -d username             # Delete password (make passwordless)
pwck                           # Verify integrity of password files
grpck                          # Verify integrity of group files
newusers                       # Create multiple users from a file
pwconv / pwunconv              # Convert to/from shadow passwords
grpconv / grpunconv            # Convert to/from shadow groups

# Account expiration
usermod -e YYYY-MM-DD username # Set account expiration date
chage -E YYYY-MM-DD username   # Alternative to set expiration
chage -M 90 username           # Set maximum password age (days)
chage -m 7 username            # Set minimum password age (days)
chage -W 14 username           # Set password expiration warning days
chage -I 30 username           # Set account inactivity lock days
chage username                 # Change user password expiry information

# Resource limits
ulimit -a                      # Show all limits for current shell
ulimit -n 2048                 # Set file descriptor limit for shell

# ACL management
getfacl file                   # Show file ACLs
setfacl -m u:username:rwx file # Add user ACL
setfacl -m g:groupname:rx file # Add group ACL
setfacl -x u:username file     # Remove specific user ACL
setfacl -b file                # Remove all ACLs
setfacl -d -m u:username:rwx directory  # Set default ACLs for new files

# Environment configuration
source ~/.bashrc               # Reload user bash configuration
source /etc/profile            # Reload system bash configuration
export PATH=$PATH:/new/path    # Add directory to PATH temporarily
export VARIABLE=value          # Set environment variable for current session
```

### SELinux User Management
```bash
# Overview and verification
semanage login -l              # List all SELinux login mappings
semanage user -l               # List all SELinux users and their MLS/MCS ranges
ps -Z                          # Show SELinux contexts for running processes
ls -Z                          # Show SELinux contexts for files and directories
sestatus                       # Show SELinux status and policy
getsebool -a | grep user       # List SELinux booleans related to users
ausearch -m avc --start recent # Search audit logs for recent SELinux denials
aureport -a                    # Generate summary report of SELinux audit events

# Create and manage SELinux users
semanage user -a -R "staff_r sysadm_r" -P user myseuser  # Create new SELinux user
semanage user -m -R "staff_r sysadm_r system_r" myseuser # Modify roles for user
semanage user -d myseuser      # Delete a SELinux user

# Map Linux users to SELinux users
semanage login -a -s user_u username               # Map Linux user to SELinux user
semanage login -m -s staff_u username              # Change SELinux mapping
semanage login -d username                         # Remove SELinux mapping
semanage login -a -s user_u -r s0:c100-c109 username # Map with specific MLS/MCS range
semanage login -a -s user_u %groupname             # Map Linux group to SELinux user
semanage login -m -S targeted -s unconfined_u __default__ # Set default SELinux user

# Change file contexts
chcon -u system_u -r system_r -t etc_t /path/to/file # Change SELinux context
restorecon -v /path/to/file                        # Restore default context
setsebool -P allow_guest_exec_content on           # Set a SELinux boolean
```

### Configuration Files
```bash
# User information files
/etc/passwd                    # User information
/etc/shadow                    # Encrypted passwords
/etc/group                     # Group information
/etc/gshadow                   # Group passwords
/etc/skel/                     # Template for new user home directories

# Authentication and authorization
/etc/sudoers                   # Sudo configuration
/etc/sudoers.d/                # Sudo configuration directory
/etc/login.defs                # Shadow password suite configuration
/etc/security/limits.conf      # User resource limits
/etc/security/limits.d/        # User resource limits directory
/etc/pam.d/                    # PAM configuration files
/etc/pam.d/common-auth         # Authentication configuration
/etc/pam.d/common-password     # Password policies

# Environment configuration
/etc/environment               # System-wide environment variable file
/etc/profile.d/                # System-wide shell scripts executed at login

# SELinux configuration
/etc/selinux/targeted/seusers  # SELinux user mapping file
/etc/selinux/targeted/contexts/users/*  # SELinux user definition files

# Common configuration examples
# /etc/security/limits.conf examples:
username hard nproc 50         # Hard limit of 50 processes
username soft nproc 30         # Soft limit of 30 processes
username hard nofile 4096      # Hard limit of 4096 open files
@developers soft memlock 64000 # Soft memlock limit for group

# Basic sudoers entries:
username ALL=(ALL) ALL         # Basic sudo access
username ALL=(ALL) NOPASSWD: ALL  # Passwordless sudo
username ALL=(ALL) /bin/systemctl restart apache2  # Command-specific sudo
%admin ALL=(ALL) ALL           # Group sudo access
```
