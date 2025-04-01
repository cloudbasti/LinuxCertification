# Linux Services and Virtualization

## Virtualization

### KVM/QEMU Commands
- `qemu-kvm -m 2048 -hda disk.img` - run virtual machine with 2GB RAM using disk.img
- `virsh list --all` - list all virtual machines (running and stopped)
- `virsh start VM_NAME` - start a virtual machine
- `virsh shutdown VM_NAME` - gracefully shutdown a VM
- `virsh destroy VM_NAME` - forcefully stop a VM (like pulling power cord)
- `virsh undefine VM_NAME` - remove VM configuration (delete VM)
- `virsh suspend VM_NAME` / `virsh resume VM_NAME` - pause/unpause VM
- `virsh setmaxmem VM_NAME 80M --config` - set maximum memory limit
- `virsh setmem VM_NAME 80M --config` - set current memory allocation
- `virsh edit VM_NAME` - edit XML configuration of a VM
- `virsh console VM_NAME` - connect to VM console
- `virt-install --name=vm1 --vcpus=1 --memory=1024 --cdrom=/path/to/iso --disk size=10` - create new VM
- `virsh autostart VM_NAME` - enable VM to start automatically at boot
- `virsh domiflist VM_NAME` - list network interfaces for VM
- `virsh attach-disk VM_NAME /path/to/disk.img vdb --cache none` - attach disk to running VM
- `virsh snapshot-create-as VM_NAME snapshot1 "First snapshot" --disk-only` - create VM snapshot
- `virsh snapshot-revert VM_NAME snapshot1` - revert to snapshot

### Docker Commands
- `docker run -d -p 8080:80 nginx` - run nginx container in background, mapping port 8080 to container port 80
- `docker ps` / `docker ps -a` - list running containers / all containers
- `docker images` - list available images
- `docker pull ubuntu:22.04` - download ubuntu image with tag 22.04
- `docker exec -it container_id bash` - get interactive shell in running container
- `docker stop container_id` / `docker rm container_id` - stop/remove container
- `docker build -t myapp:latest .` - build image from Dockerfile in current directory
- `docker-compose up -d` - start all services defined in docker-compose.yml
- `docker volume create myvolume` / `docker network create mynetwork` - create volume/network
- `docker logs container_id` - view container logs
- `docker inspect container_id` - detailed container information
- `docker network ls` - list Docker networks
- `docker network connect mynetwork container_id` - connect container to network
- `docker container prune` - remove all stopped containers
- `docker stats` - show live resource usage of containers

### LXC/LXD Commands
- `lxc-create -t download -n container1` - create LXC container
- `lxc-start -n container1` - start container
- `lxc-stop -n container1` - stop container
- `lxc-attach -n container1` - attach to container shell
- `lxc-ls --fancy` - list containers with details
- `lxc config set container1 limits.memory 512MB` - set memory limit
- `lxc file push /path/file container1/path/` - copy file to container
- `lxc snapshot container1 snapshot1` - create container snapshot

## Web Servers

### Apache Web Server
- `/etc/apache2/` - configuration directory (Debian/Ubuntu)
- `/etc/httpd/` - configuration directory (RHEL/CentOS)
- `systemctl start/stop/restart apache2` - control service
- `a2ensite site1` / `a2dissite site1` - enable/disable site
- `a2enmod rewrite` / `a2dismod rewrite` - enable/disable module
- `apache2ctl -t` - test configuration syntax
- `apache2ctl -S` - show virtual hosts
- `/etc/apache2/sites-available/000-default.conf` - default site config
- Basic virtual host configuration:
  ```
  <VirtualHost *:80>
      ServerName example.com
      ServerAlias www.example.com
      DocumentRoot /var/www/example
      ErrorLog ${APACHE_LOG_DIR}/example-error.log
      CustomLog ${APACHE_LOG_DIR}/example-access.log combined
  </VirtualHost>
  ```
- `/var/log/apache2/` - log directory

### Nginx Web Server
- `/etc/nginx/` - configuration directory
- `systemctl start/stop/restart nginx` - control service
- `nginx -t` - test configuration syntax
- `/etc/nginx/sites-available/default` - default site config
- Basic virtual host configuration:
  ```
  server {
      listen 80;
      server_name example.com www.example.com;
      root /var/www/example;
      
      location / {
          try_files $uri $uri/ =404;
      }
  }
  ```
- `nginx -s reload` - reload configuration
- `/var/log/nginx/` - log directory

## DNS Services

### BIND DNS Server
- `/etc/bind/` - configuration directory
- `named.conf.options` - global options
- `named.conf.local` - zone definitions
- Zone file configuration (`/etc/bind/db.example.com`):
  ```
  $TTL    604800
  @       IN      SOA     ns1.example.com. admin.example.com. (
                          2023042201 ; Serial
                          604800     ; Refresh
                          86400      ; Retry
                          2419200    ; Expire
                          604800 )   ; Negative Cache TTL
  @       IN      NS      ns1.example.com.
  @       IN      A       192.168.1.10
  www     IN      A       192.168.1.10
  mail    IN      A       192.168.1.20
  ```
- `systemctl start/stop/restart bind9` - control service
- `rndc reload` - reload zones without restart
- `dig @localhost example.com` - test DNS resolution
- `named-checkconf` - check configuration syntax
- `named-checkzone example.com /etc/bind/db.example.com` - check zone file

### Dnsmasq (Lightweight DNS)
- `/etc/dnsmasq.conf` - configuration file
- Basic configuration:
  ```
  domain-needed
  bogus-priv
  local=/example.com/
  address=/example.com/192.168.1.10
  dhcp-range=192.168.1.50,192.168.1.150,12h
  ```
- `systemctl start/stop/restart dnsmasq` - control service

## Remote Access Services

### SSH Server
- `/etc/ssh/sshd_config` - server configuration
- Important settings:
  ```
  Port 22
  PermitRootLogin no
  PasswordAuthentication no
  PubkeyAuthentication yes
  AllowUsers user1 user2
  ```
- `systemctl start/stop/restart sshd` - control service
- `ssh-keygen -t rsa -b 4096` - generate SSH key
- `ssh-copy-id user@host` - copy public key to server
- `~/.ssh/authorized_keys` - user's authorized public keys
- `ssh -p 2222 user@host` - connect on non-standard port
- `scp file.txt user@host:/path/` - copy file to remote host
- `sftp user@host` - SFTP session

### VNC Server
- `vncserver :1 -geometry 1280x800 -depth 24` - start VNC server
- `vncserver -kill :1` - stop VNC server
- `/etc/systemd/system/vncserver@.service` - systemd service file
- `~/.vnc/xstartup` - VNC startup script
- `vncpasswd` - set VNC password

## File Sharing Services

### NFS Server
- `/etc/exports` - exports configuration
  ```
  /shared/dir 192.168.1.0/24(rw,sync,no_subtree_check)
  /backup    *(ro,sync,no_root_squash)
  ```
- `exportfs -ra` - reload exports
- `exportfs -v` - show all exports
- `systemctl start/stop/restart nfs-kernel-server` - control service
- `showmount -e localhost` - show exported filesystems

### Samba Server
- `/etc/samba/smb.conf` - configuration file
- Basic share configuration:
  ```
  [shared]
      path = /shared/dir
      browseable = yes
      read only = no
      valid users = user1 user2
      create mask = 0660
      directory mask = 0770
  ```
- `systemctl start/stop/restart smbd` - control service
- `smbpasswd -a user1` - add Samba user
- `testparm` - check configuration syntax
- `smbclient -L //localhost` - list shares

## Email Services

### Postfix (SMTP)
- `/etc/postfix/main.cf` - main configuration
- Basic configuration:
  ```
  myhostname = mail.example.com
  mydomain = example.com
  myorigin = $mydomain
  inet_interfaces = all
  mydestination = $myhostname, localhost.$mydomain, $mydomain
  home_mailbox = Maildir/
  ```
- `systemctl start/stop/restart postfix` - control service
- `postfix check` - check configuration
- `postconf -n` - show non-default settings
- `mailq` - show mail queue
- `postsuper -d ALL` - delete all queued mail

### Dovecot (IMAP/POP3)
- `/etc/dovecot/dovecot.conf` - main configuration
- `/etc/dovecot/conf.d/` - configuration directory
- Basic settings:
  ```
  protocols = imap pop3
  mail_location = maildir:~/Maildir
  ```
- `systemctl start/stop/restart dovecot` - control service
- `doveconf -n` - show non-default settings

## Database Services

### MariaDB/MySQL
- `/etc/mysql/my.cnf` - main configuration
- `systemctl start/stop/restart mariadb` - control service
- `mysql_secure_installation` - secure initial setup
- `mysql -u root -p` - connect to server
- Basic commands:
  ```sql
  CREATE DATABASE testdb;
  CREATE USER 'testuser'@'localhost' IDENTIFIED BY 'password';
  GRANT ALL PRIVILEGES ON testdb.* TO 'testuser'@'localhost';
  FLUSH PRIVILEGES;
  ```
- `mysqladmin -u root -p status` - show server status
- `mysqldump -u root -p --all-databases > backup.sql` - backup all databases

### PostgreSQL
- `/etc/postgresql/13/main/postgresql.conf` - main configuration
- `/etc/postgresql/13/main/pg_hba.conf` - client authentication
- `systemctl start/stop/restart postgresql` - control service
- `sudo -u postgres psql` - connect as postgres user
- Basic commands:
  ```sql
  CREATE DATABASE testdb;
  CREATE USER testuser WITH PASSWORD 'password';
  GRANT ALL PRIVILEGES ON DATABASE testdb TO testuser;
  ```
- `pg_dump -U postgres testdb > backup.sql` - backup database

## Proxy and Load Balancing

### HAProxy
- `/etc/haproxy/haproxy.cfg` - configuration file
- Basic configuration:
  ```
  frontend http_front
      bind *:80
      stats uri /haproxy?stats
      default_backend http_back
      
  backend http_back
      balance roundrobin
      server web1 192.168.1.11:80 check
      server web2 192.168.1.12:80 check
  ```
- `systemctl start/stop/restart haproxy` - control service
- `haproxy -c -f /etc/haproxy/haproxy.cfg` - check configuration

### Nginx as Proxy/Load Balancer
- `/etc/nginx/nginx.conf` - main configuration file
- Basic load balancer configuration:
  ```
  http {
      upstream backend {
          server 192.168.1.11:80;
          server 192.168.1.12:80;
          # Simple round-robin by default
          # least_conn; # Least connections method
          # ip_hash; # Session persistence
      }
      
      server {
          listen 80;
          server_name example.com;
          
          location / {
              proxy_pass http://backend;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          }
      }
  }
  ```
- Reverse proxy configuration:
  ```
  server {
      listen 80;
      server_name example.com;
      
      location /api/ {
          proxy_pass http://backend_api_server:8080/;
          proxy_redirect off;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
      }
      
      location / {
          proxy_pass http://frontend_server:3000;
      }
  }
  ```
- `nginx -t` - test configuration
- `systemctl reload nginx` - reload configuration

### Squid Proxy
- `/etc/squid/squid.conf` - configuration file
- Basic configuration:
  ```
  http_port 3128
  acl localnet src 192.168.1.0/24
  http_access allow localnet
  http_access deny all
  ```
- `systemctl start/stop/restart squid` - control service
- `squid -k parse` - check configuration syntax
- `squid -k reconfigure` - reload configuration

## Job Scheduling and Control

### Cron Jobs
- User crontabs: `crontab -e` - edit current user's crontab
- System cron: `/etc/crontab` - system-wide cron configuration
- Cron directories: `/etc/cron.d/`, `/etc/cron.daily/`, `/etc/cron.hourly/`, etc.
- Cron syntax: `* * * * * command` (minute, hour, day, month, weekday)
- List cron jobs: `crontab -l`
- Remove all cron jobs: `crontab -r`
- Example: `0 3 * * * /path/to/backup.sh` - run at 3 AM daily
- View cron logs: `grep CRON /var/log/syslog`

### Anacron
- Configuration: `/etc/anacrontab`
- Format: `period delay job-id command`
- Example: `7 15 weekly-backup /path/to/backup.sh`
- Manually run jobs: `anacron -n -f`
- Periods: @daily, @weekly, @monthly

### At Jobs
- Schedule one-time job: `at 10:00 PM`
- Schedule using different formats: `at now + 1 hour`, `at teatime tomorrow`
- List pending jobs: `atq`
- Remove job: `atrm job-number`
- Check configuration: `/etc/at.allow` and `/etc/at.deny`

### Shell Job Control
- Run in background: `command &`
- List jobs: `jobs`
- Bring to foreground: `fg %1`
- Send to background: `bg %1`
- Suspend current job: `Ctrl+Z`
- Kill job: `kill %1`
- Detach from terminal: `disown %1`
- Run immune to hangups: `nohup command &`
- Execute with low priority: `nice -n 19 command`

### Systemd Timers (Modern Alternative to Cron)
- List timers: `systemctl list-timers`
- Create timer: `/etc/systemd/system/backup.timer` and `/etc/systemd/system/backup.service`
- Example timer:
