# Linux Filesystem Management Command Booklet

## 1. Create Different Filesystems

### Important Files
- `/etc/fstab` - Filesystem mount configuration
- `/proc/filesystems` - Supported filesystems
- `/proc/mounts` or `/etc/mtab` - Currently mounted filesystems

### Create Filesystems
```bash
# View block devices
lsblk
fdisk -l

# Create filesystems
mkfs.ext4 /dev/sdXY             # Create ext4 filesystem
mkfs.xfs /dev/sdXY              # Create XFS filesystem
mkfs.btrfs /dev/sdXY            # Create Btrfs filesystem
mkfs.fat -F32 /dev/sdXY         # Create FAT32 filesystem
```

### Verification Commands
```bash
lsblk -f                       # Show filesystems on all block devices
blkid /dev/sdXY                # Show UUID and filesystem type
dumpe2fs /dev/sdXY | grep -i "block size"    # ext filesystem details
xfs_info /dev/sdXY             # XFS filesystem info
tune2fs -l /dev/sdXY           # View ext filesystem parameters
```

## 2. Mount Filesystems at Boot

### Important Files
- `/etc/fstab` - Boot-time mount configuration

### /etc/fstab Format
```
<device> <mountpoint> <filesystem-type> <options> <dump> <fsck-order>

# Examples:
UUID=1234-5678-90ab-cdef /mnt/data1 ext4 defaults 0 2
/dev/sdc1 /mnt/data2 xfs defaults 0 2
/dev/sdd1 /mnt/data3 btrfs defaults,compress=zstd 0 2
```

### Common Mount Options
- `defaults` - Default options (rw,suid,dev,exec,auto,nouser,async)
- `noauto` - Don't mount at boot
- `ro` / `rw` - Read-only / Read-write
- `user` / `nouser` - Allow / Disallow user mounts
- `exec` / `noexec` - Allow / Prevent binary execution
- `sync` / `async` - Synchronous / asynchronous I/O
- `nofail` - Ignore device if not present

### Mount Commands
```bash
mkdir -p /mnt/data1            # Create mount point
mount -a                       # Mount all in /etc/fstab
mount /dev/sdXY /mnt/data1     # Mount specific device
mount -o remount,rw /mnt/data1 # Remount with new options
umount /mnt/data1              # Unmount by mount point
umount /dev/sdXY               # Unmount by device
umount -l /mnt/data1           # Lazy unmount (when device is busy)
```

### Verification Commands
```bash
mount | grep "/mnt/data"       # Show mounted filesystems
df -h                          # Show disk space usage
findmnt                        # Show all mounted filesystems in tree
findmnt --verify --verbose     # Verify fstab syntax
systemctl list-units --type=mount  # Show systemd mount units
```

## 3. Remote Filesystems

### NFS Server

#### Important Files
- `/etc/exports` - NFS export configuration

#### Configuration and Commands
```bash
# Install NFS server
apt install nfs-kernel-server

# /etc/exports syntax:
/shared/dir 192.168.1.0/24(rw,sync,no_subtree_check)
/backup    host.example.com(ro,sync,no_root_squash)

# Apply configuration
exportfs -ra                   # Refresh exports
exportfs -v                    # View current exports

# Start/manage service
systemctl status nfs-kernel-server
systemctl start nfs-kernel-server
systemctl enable nfs-kernel-server
```

### NFS Client

#### Important Files
- `/etc/fstab` - For permanent mounts

#### Commands
```bash
# Install NFS client
apt install nfs-common

# Mount NFS share
mkdir -p /mnt/nfs
mount -t nfs server:/shared/dir /mnt/nfs
mount -t nfs -o ro,nosuid server:/shared/dir /mnt/nfs

# Add to /etc/fstab
server:/shared/dir /mnt/nfs nfs defaults,_netdev 0 0
```

### Verification Commands
```bash
showmount -e server            # List exports on server
nfsstat                        # NFS statistics
rpcinfo -p server              # RPC services
mount | grep nfs               # Show mounted NFS filesystems
df -h | grep nfs               # Show NFS filesystem usage
```

## 4. Partitions

### Important Files
- `/proc/partitions` - Current partition information
- `/dev/sd*`, `/dev/nvme*` - Disk device files

### Partition Commands
```bash
# View partitions
fdisk -l                       # List all partitions
parted -l                      # List all partitions (alternative)
lsblk                          # List block devices in tree format
cat /proc/partitions           # Show partition info from kernel

# Create partitions with fdisk (interactive)
fdisk /dev/sdX                 # Start fdisk (commands: n=new, d=delete, t=type, w=write)

# Create partitions with parted (one-liners)
parted -s /dev/sdX mklabel gpt                           # Create GPT table
parted -s /dev/sdX mklabel msdos                         # Create MBR table
parted -s /dev/sdX mkpart primary ext4 1MiB 10GiB        # Create partition
parted -s /dev/sdX mkpart primary linux-swap 10GiB 12GiB # Create swap partition
parted -s /dev/sdX set 1 boot on                         # Set boot flag

# Create partitions with sfdisk (script-friendly)
echo ',,L,*' | sfdisk /dev/sdX # Create one Linux partition
echo -e "label: gpt\n,512M,U,*\n,10G,L\n,5G,S\n," | sfdisk /dev/sdX # Multiple partitions
```

### Verification Commands
```bash
sfdisk -l /dev/sdX             # List partitions
sgdisk -p /dev/sdX             # List GPT partitions
parted /dev/sdX print          # Show partition table
parted /dev/sdX unit MiB print # Show sizes in MiB
```

## 5. Swap Space

### Important Files
- `/proc/swaps` - Current swap information
- `/proc/sys/vm/swappiness` - Kernel swappiness parameter
- `/etc/fstab` - For permanent swap configuration

### Swap Commands
```bash
# Create swap partition
mkswap /dev/sdXY               # Format as swap
swapon /dev/sdXY               # Enable swap
swapoff /dev/sdXY              # Disable swap

# Create swap file
dd if=/dev/zero of=/swapfile bs=1M count=1024    # Create 1GB file
chmod 600 /swapfile            # Set permissions
mkswap /swapfile               # Format as swap
swapon /swapfile               # Enable swap

# Add to /etc/fstab
/dev/sdXY none swap sw 0 0     # Swap partition
/swapfile none swap sw 0 0     # Swap file

# Adjust swappiness (0-100, lower = less swapping)
sysctl vm.swappiness=10        # Temporary
echo "vm.swappiness=10" >> /etc/sysctl.conf  # Permanent
```

### Verification Commands
```bash
free -h                        # Show memory and swap usage
swapon --show                  # Show swap devices
cat /proc/swaps                # Raw swap info from kernel
cat /proc/sys/vm/swappiness    # Current swappiness value
vmstat 1                       # Virtual memory statistics
```

## 6. Logical Volume Management (LVM)

### Important Files
- `/etc/lvm/lvm.conf` - LVM configuration
- `/dev/mapper/` - Device mapper files

### Physical Volumes (PV)
```bash
pvcreate /dev/sdX1 /dev/sdX2   # Create PVs
pvs                            # List PVs (brief)
pvdisplay                      # List PVs (detailed)
pvremove /dev/sdX1             # Remove PV
pvscan                         # Scan for PVs
```

### Volume Groups (VG)
```bash
vgcreate vg_name /dev/sdX1 /dev/sdX2  # Create VG
vgextend vg_name /dev/sdX3     # Add PV to VG
vgreduce vg_name /dev/sdX1     # Remove PV from VG
vgs                            # List VGs (brief)
vgdisplay                      # List VGs (detailed)
vgremove vg_name               # Remove VG
vgscan                         # Scan for VGs
```

### Logical Volumes (LV)
```bash
lvcreate -L 10G -n lv_name vg_name   # Create 10GB LV
lvcreate -l 80%VG -n lv_name vg_name # Use 80% of VG
lvs                            # List LVs (brief)
lvdisplay                      # List LVs (detailed)
lvextend -L +5G /dev/vg_name/lv_name  # Extend by 5GB
lvextend -l +100%FREE /dev/vg_name/lv_name # Use all free space
lvreduce -L -5G /dev/vg_name/lv_name  # Reduce by 5GB (unmount first!)
lvremove /dev/vg_name/lv_name  # Delete LV
lvscan                         # Scan for LVs
```

### Filesystem Operations with LVM
```bash
# Resize ext4 filesystem after extending LV (online)
resize2fs /dev/vg_name/lv_name

# Resize XFS filesystem (only grow, not shrink)
xfs_growfs /mount/point

# Create/manage snapshots
lvcreate -L 1G -s -n snap_name /dev/vg_name/lv_name  # Create snapshot
mount -o ro /dev/vg_name/snap_name /mnt/snapshot     # Mount snapshot
lvremove /dev/vg_name/snap_name                      # Remove snapshot
```

### Verification Commands
```bash
dmsetup ls                     # List device mapper devices
lsblk | grep -E 'lvm|LVM'      # Show LVM in block devices
vgdisplay vg_name -v | grep "Free PE"  # Show free space
```




