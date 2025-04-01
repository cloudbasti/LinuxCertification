# Linux Networking and Firewalls Reference

## Basic Network Configuration

### Network Interface Management
- `ip link` - show network interfaces
- `ip link show dev eth0` - show specific interface details
- `ip addr` - show IP addresses for all interfaces
- `ip addr show dev eth0` - show IP addresses for specific interface
- `ip link set dev eth0 up` - bring interface up
- `ip link set dev eth0 down` - bring interface down
- `ifconfig` - legacy command to show/manage network interfaces
- `ethtool eth0` - show/change ethernet card settings

### IP Configuration
- `ip addr add 192.168.1.10/24 dev eth0` - assign IP address
- `ip addr del 192.168.1.10/24 dev eth0` - remove IP address
- `ip route` - show routing table
- `ip route add default via 192.168.1.1` - add default gateway
- `ip route del default` - remove default route
- `ip neigh` - show ARP cache/neighbor table

### Dynamic IP Configuration
- `/etc/dhcp/dhclient.conf` - DHCP client configuration
- `dhclient eth0` - obtain IP address via DHCP
- `dhclient -r eth0` - release DHCP lease

### Network Configuration Files
- `/etc/network/interfaces` - Debian/Ubuntu legacy network config
  ```
  auto eth0
  iface eth0 inet static
      address 192.168.1.10
      netmask 255.255.255.0
      gateway 192.168.1.1
  ```
- `/etc/netplan/*.yaml` - Ubuntu Netplan configuration
  ```yaml
  network:
    version: 2
    ethernets:
      ens3:
        addresses: [192.168.1.10/24]
        gateway4: 192.168.1.1
        nameservers:
          addresses: [8.8.8.8, 8.8.4.4]
  ```
- `/etc/sysconfig/network-scripts/ifcfg-eth0` - RHEL/CentOS configuration
  ```
  DEVICE=eth0
  BOOTPROTO=static
  IPADDR=192.168.1.10
  NETMASK=255.255.255.0
  GATEWAY=192.168.1.1
  ONBOOT=yes
  ```

### NetworkManager
- `nmcli device status` - show device status
- `nmcli connection show` - show connections
- `nmcli connection show --active` - show active connections
- `nmcli connection add type ethernet con-name "Office" ifname eth0 ipv4.method manual ipv4.addresses "192.168.1.10/24" ipv4.gateway "192.168.1.1" ipv4.dns "8.8.8.8"` - add static connection
- `nmcli connection modify "Office" ipv4.addresses "192.168.1.20/24"` - modify connection
- `nmcli connection up "Office"` - activate connection
- `nmcli connection down "Office"` - deactivate connection
- `nmcli connection delete "Office"` - delete connection
- `nmtui` - text user interface for NetworkManager

## Name Resolution

### DNS Configuration
- `/etc/resolv.conf` - DNS resolver configuration
  ```
  nameserver 8.8.8.8
  nameserver 8.8.4.4
  search example.com
  ```
- `/etc/hosts` - static host to IP mapping
  ```
  127.0.0.1    localhost
  192.168.1.10 server.example.com server
  ```
- `/etc/nsswitch.conf` - name service switch configuration
  ```
  hosts: files dns
  ```

### DNS Utilities
- `dig example.com` - DNS lookup utility
- `dig @8.8.8.8 example.com` - query specific DNS server
- `dig -x 8.8.8.8` - reverse DNS lookup
- `host example.com` - simple DNS lookup
- `nslookup example.com` - interactive DNS lookup
- `getent hosts example.com` - query hosts according to nsswitch.conf

## Network Diagnostics

### Connectivity Testing
- `ping host` - basic connectivity test
- `ping -c 4 host` - send 4 packets only
- `ping6 ipv6address` - ping IPv6 address
- `traceroute host` - trace packet route
- `tracepath host` - trace path (no root required)
- `mtr host` - better traceroute with statistics
- `nc -zv host port` - test if port is open
- `telnet host port` - test connection to port

### Network Inspection
- `ss -tuln` - show listening TCP/UDP ports
- `ss -tap` - show all TCP connections with processes
- `netstat -tuln` - legacy command to show listening ports
- `netstat -antp` - show all TCP connections with PIDs
- `lsof -i` - list processes with open network files
- `lsof -i :80` - show processes using port 80
- `tcpdump -i eth0` - capture packets on interface
- `tcpdump -i eth0 port 80` - capture HTTP traffic
- `tcpdump -i eth0 host 192.168.1.10` - capture traffic for specific host
- `nmap -p 1-1000 host` - scan ports on remote host
- `nmap -sV host` - scan for services and versions

### Network Performance
- `iperf3 -s` - run as server for bandwidth test
- `iperf3 -c server` - run as client and test bandwidth
- `speedtest-cli` - test internet connection speed

## Firewalls

### UFW (Uncomplicated Firewall)
- `ufw status` - show firewall status
- `ufw enable` - enable firewall
- `ufw disable` - disable firewall
- `ufw allow 22/tcp` - allow SSH
- `ufw allow http` - allow HTTP (port 80)
- `ufw allow from 192.168.1.0/24` - allow from network
- `ufw allow from 192.168.1.0/24 to any port 22` - allow SSH from network
- `ufw deny 23/tcp` - deny telnet
- `ufw delete allow 80/tcp` - remove rule
- `ufw reset` - reset to default configuration
- `ufw logging on` - enable logging
- `ufw status numbered` - list rules with numbers
- `ufw delete 2` - delete rule by number

### firewalld (RHEL/CentOS/Fedora)
- `firewall-cmd --state` - show firewall state
- `firewall-cmd --get-default-zone` - show default zone
- `firewall-cmd --get-active-zones` - show active zones
- `firewall-cmd --list-all` - list all configurations
- `firewall-cmd --list-all-zones` - list all zones configurations
- `firewall-cmd --add-service=http` - allow HTTP temporarily
- `firewall-cmd --add-service=http --permanent` - allow HTTP permanently
- `firewall-cmd --add-port=8080/tcp --permanent` - allow custom port
- `firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" service name="ssh" accept' --permanent` - add rich rule
- `firewall-cmd --remove-service=http --permanent` - remove service
- `firewall-cmd --reload` - reload configuration

### iptables (Low-level)
- `iptables -L` - list all rules
- `iptables -L -v` - list all rules with details
- `iptables -L INPUT` - list input chain rules
- `iptables -A INPUT -p tcp --dport 22 -j ACCEPT` - allow SSH
- `iptables -A INPUT -p tcp --dport 80 -j ACCEPT` - allow HTTP
- `iptables -A INPUT -s 192.168.1.0/24 -j ACCEPT` - allow from network
- `iptables -A INPUT -j DROP` - drop all other traffic
- `iptables -D INPUT 3` - delete rule number 3
- `iptables -F` - flush all rules
- `iptables-save > /etc/iptables/rules.v4` - save rules
- `iptables-restore < /etc/iptables/rules.v4` - restore rules
- `iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080` - port forwarding

### nftables (Successor to iptables)
- `nft list tables` - list all tables
- `nft list table inet filter` - list specific table
- `nft add rule inet filter input tcp dport 22 accept` - allow SSH
- `nft add chain inet filter input { type filter hook input priority 0 \; policy drop \; }` - create chain
- `nft flush ruleset` - flush all rules
- `/etc/nftables.conf` - configuration file
- `nft -f /etc/nftables.conf` - load configuration

## Advanced Networking

### Network Bonding
- `/etc/modprobe.d/bonding.conf` - kernel module configuration
- `/etc/sysconfig/network-scripts/ifcfg-bond0` - bond interface configuration (RHEL)
- Create bond using Netplan:
  ```yaml
  network:
    version: 2
    bonds:
      bond0:
        interfaces: [eth0, eth1]
        parameters:
          mode: active-backup
        addresses: [192.168.1.10/24]
        gateway4: 192.168.1.1
  ```
- `cat /proc/net/bonding/bond0` - show bond status

### Network Bridging
- `brctl show` - show bridges (legacy command)
- `ip link add name br0 type bridge` - create bridge
- `ip link set dev eth0 master br0` - add interface to bridge
- `ip link set dev br0 up` - bring bridge up
- Create bridge using Netplan:
  ```yaml
  network:
    version: 2
    bridges:
      br0:
        interfaces: [eth0]
        addresses: [192.168.1.10/24]
        gateway4: 192.168.1.1
  ```

### VLANs
- `ip link add link eth0 name eth0.10 type vlan id 10` - create VLAN
- `ip addr add 192.168.10.1/24 dev eth0.10` - assign IP to VLAN
- Create VLAN using Netplan:
  ```yaml
  network:
    version: 2
    ethernets:
      eth0: {}
    vlans:
      eth0.10:
        id: 10
        link: eth0
        addresses: [192.168.10.1/24]
  ```

### IP Tunneling
- `ip tunnel add tun0 mode gre remote 203.0.113.2 local 203.0.113.1` - create GRE tunnel
- `ip addr add 10.0.0.1/30 dev tun0` - assign IP to tunnel
- `ip link set dev tun0 up` - bring tunnel up

## Network Services

### SSH Server
- `/etc/ssh/sshd_config` - SSH server configuration
- Key settings:
  ```
  Port 22
  PermitRootLogin no
  PasswordAuthentication no
  PubkeyAuthentication yes
  ```
- `systemctl restart sshd` - restart SSH server
- `ssh-keygen -t rsa -b 4096` - generate SSH key
- `ssh-copy-id user@host` - copy public key to server

### DHCP Server
- `/etc/dhcp/dhcpd.conf` - DHCP server configuration
  ```
  subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option routers 192.168.1.1;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
  }
  ```
- `systemctl restart isc-dhcp-server` - restart DHCP server

### NAT/Routing
- `echo 1 > /proc/sys/net/ipv4/ip_forward` - enable IP forwarding
- `/etc/sysctl.conf` setting for persistence:
  ```
  net.ipv4.ip_forward = 1
  ```
- `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` - enable NAT
- `iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT` - allow forwarding

### VPN
- OpenVPN server config example (`/etc/openvpn/server.conf`):
  ```
  port 1194
  proto udp
  dev tun
  ca ca.crt
  cert server.crt
  key server.key
  dh dh2048.pem
  server 10.8.0.0 255.255.255.0
  push "redirect-gateway def1"
  push "dhcp-option DNS 8.8.8.8"
  ```
- `systemctl restart openvpn@server` - restart OpenVPN server

## Security Hardening

### Network Security Basics
- Disable unused services:
  ```
  systemctl disable telnet
  systemctl stop telnet
  ```
- Secure SSH:
  ```
  # In /etc/ssh/sshd_config
  Protocol 2
  PermitRootLogin no
  MaxAuthTries 3
  ```
- Use strong encryption for SSH:
  ```
  # In /etc/ssh/sshd_config
  Ciphers aes256-ctr,aes192-ctr,aes128-ctr
  MACs hmac-sha2-512,hmac-sha2-256
  ```

### Port Security
- `fail2ban-client status` - check fail2ban status
- `fail2ban-client status sshd` - check ssh jail status
- `/etc/fail2ban/jail.conf` - fail2ban configuration
- Example jail configuration:
  ```
  [sshd]
  enabled = true
  port = ssh
  filter = sshd
  logpath = /var/log/auth.log
  maxretry = 3
  bantime = 3600
  ```

### Intrusion Detection
- `rkhunter --check` - check for rootkits
- `chkrootkit` - alternative rootkit checker
- `aide --check` - check file integrity
- `/etc/aide/aide.conf` - AIDE configuration

### Traffic Monitoring
- `netstat -st` - display network statistics
- `iptraf-ng` - interactive network monitor
- `vnstat -i eth0` - network traffic monitor
- `nethogs` - show process bandwidth usage
