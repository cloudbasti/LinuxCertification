# Linux Command Reference - Organized by Function

## System Configuration

### Host Configuration
- `hostnamectl` - display host information
- `hostnamectl set-hostname <name>` - change hostname
- `uname -a` - show kernel and system information
- `lsb_release -a` - show distribution information
- `cat /etc/os-release` - detailed OS information

### Time Management
- Services: `chrony` or `systemd-timesyncd`
- Config files: `/etc/chrony.conf` or `/etc/systemd/timesyncd.conf`
- Check status: `systemctl status chronyd` or `systemctl status systemd-timesyncd`
- `timedatectl` - display time and date settings
- `timedatectl set-timezone Europe/Bucharest` - change timezone
- `timedatectl list-timezones | grep Europe` - find available timezones
- NTP configuration: `/etc/systemd/timesyncd.conf`
  ```
  [Time]
  NTP=0.europe.pool.ntp.org 1.europe.pool.ntp.org
  ```
- Restart time service: `systemctl restart systemd-timesyncd`

### Environment Configuration
- `source ~/.bashrc` / `source /etc/profile` - reload user/system bash configuration
- `/etc/profile.d/` - directory containing system-wide shell scripts executed at login
- `env` - display all environment variables
- `/etc/environment` - system-wide environment variable file
- `echo $PATH` - display the current executable search path
- `export PATH=$PATH:/new/path` - add directory to PATH temporarily
- `export VARIABLE=value` - set environment variable for current session

### Kernel Parameters
- `/etc/sysctl.conf` - persistent kernel parameter configuration 
- `/etc/sysctl.d/*.conf` - modular kernel parameter files
- `sysctl -a` - show all kernel parameters
- `sysctl vm.swappiness` - show specific parameter
- `sysctl -w vm.swappiness=10` - set parameter temporarily
- `sysctl -p` - load settings from /etc/sysctl.conf
- `sysctl -p /etc/sysctl.d/99-custom.conf` - load specific config file

## System Resource Management

### Process Control
- `ps aux` - list all processes with details
- `ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%cpu` - processes sorted by CPU usage
- `top` / `htop` - interactive process viewer
- `pgrep firefox` - find process IDs by name
- `pkill -9 firefox` - forcefully kill processes by name
- `kill -l` - list all signal names
- `kill -15 PID` - gracefully terminate process (SIGTERM)
- `kill -9 PID` - forcefully kill process (SIGKILL)
- `renice +10 -p PID` - reduce process priority
- `nice -n 10 command` - start command with lower priority
- `nohup command &` - run command immune to hangups
- `timeout 10s command` - run command with time limit

### Memory Management
- `free -h` - display free and used memory
- `vmstat 1` - report virtual memory statistics
- `cat /proc/meminfo` - detailed memory information
- `dmesg | grep -i memory` - kernel memory messages
- `watch -n 1 free -m` - monitor memory in real-time
- `sync && echo 3 > /proc/sys/vm/drop_caches` - clear page cache
- `cat /proc/sys/vm/swappiness` - check swappiness
- `sysctl vm.swappiness=10` - reduce swap usage

### CPU Monitoring
- `mpstat -P ALL` - display per-processor statistics
- `uptime` - show load average
- `lscpu` - display CPU information
- `cat /proc/cpuinfo` - detailed CPU info
- `stress --cpu 8` - stress test with 8 CPU workers
- `taskset -c 0,1 command` - run command on specific CPU cores
- `cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor` - check CPU governor

### Service Management
- `systemctl status service` - service status with recent logs
- `systemctl start/stop/restart/reload service` - service lifecycle
- `systemctl enable/disable service` - boot time activation
- `systemctl list-units --state=failed` - show failed services
- `systemctl mask service` - completely prevent service from starting
- `systemd-analyze blame` - list units by startup time
- `systemctl list-dependencies service` - show service dependencies



## System Monitoring and Troubleshooting

### Hardware Information
- `dmidecode` - hardware information from BIOS
- `lsmod` - list loaded kernel modules
- `lspci` - list PCI devices
- `lsusb` - list USB devices
- `lsof` - list open files and processes
- `lsof -i :80` - show processes using port 80
- `lsblk` - list block devices

### Performance Monitoring
- `uptime` - show load average
- `iostat -xz 1` - disk I/O statistics
- `iotop` - I/O monitoring top-like interface
- `sar -u 1 10` - CPU usage sampled every 1 second, 10 times
- `sar -r 1 10` - memory usage
- `sar -b 1 10` - I/O operations
- `pidstat 1` - per-process statistics
- `netstat -tulpn` - listening ports and processes
- `ss -tuln` - alternative to netstat (faster)
- `dstat` - versatile resource statistics tool

### System Logging
- `/var/log/` - main log directory
- `/etc/rsyslog.conf` - syslog configuration
- `/etc/rsyslog.d/` - modular syslog configuration
- `journalctl` - query systemd journal
- `journalctl -u apache2` - logs for specific service
- `journalctl --since "1 hour ago"` - time-based filtering
- `journalctl -f` - follow new log entries
- `logger "Test message"` - add entry to syslog

### Troubleshooting Tools
- `dmesg` - kernel ring buffer messages
- `dmesg -T` - show kernel messages with human-readable timestamps
- `dmesg | grep -i error` - find errors in kernel messages
- `journalctl -xe` - view and follow systemd journal
- `journalctl -u service` - logs for specific service
- `strace command` - trace system calls
- `ltrace command` - trace library calls
- `history` - command history
- `last` - show last logins
- `w` - show who is logged in and what they're doing
