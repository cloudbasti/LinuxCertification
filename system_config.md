## System Information
- `hostnamectl` - display host information
- `hostnamectl set-hostname <name>` - change hostname

  ## Time Management
- Services: `chrony` or `systemd-timesyncd`
- Config files: `/etc/chrony.conf` or `/etc/systemd/timesyncd.conf`
- Check status: `systemctl status chronyd` or `systemctl status systemd-timesyncd`
- `sudo timedatectl set-timezone Europe/Bucharest`
- `timedatectl list-timezones | grep Europe`
- NTP configuration: `/etc/systemd/timesyncd.conf`
  ```
  [Time]
  NTP=0.europe.pool.ntp.org 1.europe.pool.ntp.org
  ```
- Restart time service: `sudo systemctl restart systemd-timesyncd`

- ## Environment Configuration
- `source ~/.bashrc` / `source /etc/profile` - reload user/system bash configuration
- `/etc/profile.d/` - directory containing system-wide shell scripts executed at login
- `env` - display all environment variables
- `/etc/environment` - system-wide environment variable file
- `echo $PATH` - display the current executable search path
- `export PATH=$PATH:/new/path` - add directory to PATH temporarily
- `export VARIABLE=value` - set environment variable for current session
