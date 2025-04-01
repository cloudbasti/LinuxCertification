# Linux Command Reference



## File Operations
- `ls -i` - shows inode information
- `ls --full-time` - display timestamps in full format
- `cp -R /tmp/Invoice/ /home/bob` - copy all contents recursively
- `cp --preserve /home/bob/myfile.txt /home/bob/data` - preserve attributes
- `mv /home/bob/lfcs/* /home/bob/new-data/` - move all contents, not directory itself
- `mv /home/bob/new_file /home/bob/old_file` - rename a file
- `ln -s /tmp /home/bob/link_to_tmp` - create symbolic link
- `ln /path/to/original/file /path/to/link` - create hard link
- `mkdir -p /tmp/1/2/3/4/5/6/7/8/9` - create with all non-existing subdirectories

## Find Command
- `find $HOME -mtime 0` - modified in last 24 hours
- `find . -perm 664` - exactly these permissions
- `find -perm -664` - at least these permissions
- `find -perm /664` - files/directories with any of these permissions
- `find -perm -u+r,g+r` - at least owner AND group can read
- `find -perm /u+r,g+r` - either owner OR group can read
- `find . -perm -444 -perm /222 ! -perm /111` - readable by everyone, writable by someone, not executable by anyone
- `find . -perm /u+w,g+w ! -perm /o+w` - files writable by owner OR group, but not by others
- `sudo find / -type f ! -perm /4000` - find all files with SETUID deactivated
- `sudo find / -type f -perm -4000` - find all files with SETUID activated
- `sudo find / -type f -perm -4000 -delete` - delete all files found
- `sudo find /opt/findme/ -type f -perm /u=s -exec rm {} \;` - find files with setuid and remove them
- `find / -type f -size +1k -exec cp {} /opt/ \;` - find files by size and copy
- `find / -type f -size +1M -size -5M` - find files in size range
- `find / -type f -size +1k -not -path "/proc/*" -not -path "/sys/*"` - exclude directories
- `find / -type d -empty -delete` - find and delete empty directories

## Important Commands
- Write with sudo permissions:
  - `sudo sh -c 'find /opt/findme/ -perm -100 > /opt/foundthem.txt'`
  - `find /opt/findme/ -perm /u=x -type f | sudo tee /opt/foundthem.txt`

## Job Scheduling
- User cron: `sudo -u john crontab -e`
- Cron syntax: `0 17 * * 3 john command`
- System cron: `/etc/crontab` and `/etc/cron.d/`
- Predefined intervals: `/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/`
- Anacron: `/etc/anacrontab` - `7 15 weekly-cleanup command`
- At jobs: `at 5pm next wednesday`
- Edit crontab: `crontab -e` (stored in `/var/spool/cron/crontabs/`)



