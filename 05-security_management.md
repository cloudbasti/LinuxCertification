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

I'll expand your Security Management section with more advanced topics and practical security tools:

### System Hardening
- `fail2ban-client status` - show Fail2ban status
- `fail2ban-client status sshd` - show SSH jail status
- `/etc/fail2ban/jail.conf` - fail2ban configuration
- `tune2fs -c 20 -i 30d /dev/sdX` - set filesystem check frequency
- `sysctl kernel.dmesg_restrict=1` - restrict dmesg access
- `sysctl kernel.kptr_restrict=2` - hide kernel pointers
- `systemd-analyze security service` - review systemd service security

### Password and Authentication Security
- `pwscore` - check password strength
- `/etc/pam.d/common-password` - PAM password policy
- `pam_tally2 --user=username` - show failed login attempts
- `pam_tally2 --user=username --reset` - reset failed login counter
- `google-authenticator` - set up 2FA for SSH
- `/etc/security/pwquality.conf` - password quality configuration

### Encryption
- `cryptsetup luksFormat /dev/sdX` - encrypt partition with LUKS
- `cryptsetup luksOpen /dev/sdX secretdata` - open encrypted partition
- `cryptsetup luksAddKey /dev/sdX` - add backup key
- `cryptsetup luksDump /dev/sdX` - show LUKS header info
- `ecryptfs-setup-private` - set up encrypted home directory
- `gpg --gen-key` - generate GPG key pair
- `gpg -e -r recipient@example.com file` - encrypt file for recipient
- `openssl enc -aes-256-cbc -salt -in file -out file.enc` - encrypt with OpenSSL

### Network Security Tools
- `nmap -sS 192.168.1.0/24` - stealthy port scan
- `nikto -h http://example.com` - web server security scanner
- `lynis audit system` - security auditing tool
- `rkhunter --check` - rootkit scanner
- `chkrootkit` - alternative rootkit checker
- `tcpdump -i eth0 not port 22` - capture non-SSH packets
- `iptables -I INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --set` - log connection attempts
- `iptables -I INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --update --seconds 60 --hitcount 4 -j DROP` - basic brute-force protection

### File Integrity
- `aide --init` - initialize AIDE database
- `aide --check` - check for file changes
- `/etc/aide/aide.conf` - AIDE configuration
- `tripwire --init` - initialize Tripwire database
- `tripwire --check` - check for file changes
- `sha256sum file > file.sha256` - create checksum
- `sha256sum -c file.sha256` - verify checksum

### Security Auditing and Logs
- `aureport --auth` - authentication report from audit logs
- `aureport --login` - login report
- `ausearch -m avc` - search for SELinux AVC messages
- `wazuh-agent status` - check security monitoring agent
- `journalctl -xf` - follow system logs
- `logwatch --range today` - summarize today's logs
- `tiger` - automated security audit

### Certificate Management
- `openssl req -new -x509 -nodes -out server.crt -keyout server.key` - generate self-signed certificate
- `openssl x509 -text -in server.crt` - examine certificate
- `certbot --apache` - obtain Let's Encrypt certificate for Apache
- `certbot --nginx` - obtain Let's Encrypt certificate for Nginx
- `certbot renew` - renew certificates
- `update-ca-certificates` - update system CA certificates

### Secure Remote Access
- `ssh-keygen -t ed25519` - generate secure SSH key
- `ssh-copy-id user@host` - copy SSH key to server
- `/etc/ssh/sshd_config` settings:
  ```
  PermitRootLogin no
  PasswordAuthentication no
  AuthenticationMethods publickey
  AllowUsers user1 user2
  Protocol 2
  X11Forwarding no
  ```
- `mosh user@host` - mobile shell with better roaming
- `/etc/hosts.allow` and `/etc/hosts.deny` - TCP wrappers control

This expanded security section covers more aspects of Linux security and provides additional tools for your practice sessions. It includes both basic security measures and more advanced techniques used in real-world environments.
