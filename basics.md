# File Operations and Management

## Basic File Operations
- `ls -la` - list all files with details
- `ls -i` - show inode information
- `ls --full-time` - display timestamps in full format
- `cp -Rp source/ dest/` - recursive copy preserving attributes
- `mv source target` - move or rename files
- `mv /home/bob/lfcs/* /home/bob/new-data/` - move all contents, not directory itself
- `ln -s target linkname` - create symbolic link
- `ln target linkname` - create hard link
- `mkdir -p path/to/dir` - create with parent directories
- `touch file` - create empty file or update timestamp
- `stat file` - display detailed file information
- `file myfile` - determine file type

## File Archiving and Compression
- `tar -cf archive.tar files` - create uncompressed tar archive
- `tar -czf archive.tar.gz files` - create gzip compressed archive
- `tar -cjf archive.tar.bz2 files` - create bzip2 compressed archive
- `tar -xf archive.tar -C /target` - extract to specific directory
- `tar -tvf archive.tar` - list contents without extracting
- `gzip file` - compress file (replaces original)
- `gunzip file.gz` - decompress gzip file
- `bzip2 file` - compress using bzip2 (better compression)
- `bunzip2 file.bz2` - decompress bzip2 file
- `xz file` - compress using xz (best compression)
- `unxz file.xz` - decompress xz file
- `zip archive.zip files` - create zip archive
- `unzip archive.zip` - extract zip files

## File Encryption
- `gpg -c file` - encrypt file with password
- `gpg file.gpg` - decrypt file
- `openssl enc -aes-256-cbc -in file -out file.enc` - encrypt with OpenSSL
- `openssl enc -d -aes-256-cbc -in file.enc -out file` - decrypt
- `dd if=/dev/urandom of=key bs=32 count=1` - generate random key
- `cryptsetup luksFormat /dev/sdX` - encrypt partition with LUKS
- `cryptsetup luksOpen /dev/sdX secretdata` - open encrypted partition
- `cryptsetup luksClose secretdata` - close encrypted partition

## Find Command also to find directories
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

## Important Commands to write to a file after using find
- Write with sudo permissions:
  - `sudo sh -c 'find /opt/findme/ -perm -100 > /opt/foundthem.txt'`
  - `find /opt/findme/ -perm /u=x -type f | sudo tee /opt/foundthem.txt`





