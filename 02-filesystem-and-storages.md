# Linux Filesystem Management Command Booklet

## 1. Filesystems

### Overview and Verification
```bash
# View supported and mounted filesystems
cat /proc/filesystems          # Supported filesystems
cat /proc/mounts               # Currently mounted filesystems
mount                          # Show all mounted filesystems
findmnt                        # Show mounted filesystems in tree format
lsblk -f                       # Show filesystems on all block devices
df -h                          # Show disk space usage of mounted filesystems
blkid                          # Show UUID and filesystem types of all block devices
blkid /dev/sdXY                # Show UUID and filesystem type of specific device
```

### Create and Manage Filesystems
```bash
# View block devices
lsblk
fdisk -l

# Create filesystems
mkfs.ext4 /dev/sdXY             # Create ext4 filesystem
mkfs.xfs /dev/sdXY              # Create XFS filesystem
mkfs.btrfs /dev/sdXY            # Create Btrfs filesystem
mkfs.fat -F32 /dev/sdXY         # Create FAT32 filesystem

# Mount/unmount filesystems
mkdir -p /mnt/data1             # Create mount point
mount /dev/sdXY /mnt/data1      # Mount specific device
mount -o remount,rw /mnt/data1  # Remount with new options
mount -a                        # Mount all in /etc/fstab
umount /mnt/data1               # Unmount by mount point
umount /dev/sdXY                # Unmount by device
umount -l /mnt/data1            # Lazy unmount (when device is busy)
```

### Configuration Files
```bash
# /etc/fstab format
<device> <mountpoint> <filesystem-type> <options> <dump> <fsck-order>

# Examples:
UUID=1234-5678-90ab-cdef /mnt/data1 ext4 defaults 0 2
/dev/sdc1 /mnt/data2 xfs defaults 0 2
/dev/sdd1 /mnt/data3 btrfs defaults,compress=zstd 0 2

# Common mount options
# defaults - Default options (rw,suid,dev,exec,auto,nouser,async)
# noauto - Don't mount at boot
# ro / rw - Read-only / Read-write
# user / nouser - Allow / Disallow user mounts
# exec / noexec - Allow / Prevent binary execution
# sync / async - Synchronous / asynchronous I/O
# nofail - Ignore device if not present
```

### Advanced Verification
```bash
dumpe2fs /dev/sdXY | grep -i "block size"  # ext filesystem details
tune2fs -l /dev/sdXY           # View ext filesystem parameters
xfs_info /dev/sdXY             # XFS filesystem info
findmnt --verify --verbose     # Verify fstab syntax
systemctl list-units --type=mount  # Show systemd mount units
```

## 2. Partitions

### Overview and Verification
```bash
fdisk -l                       # List all partitions
parted -l                      # List all partitions (alternative)
lsblk                          # List block devices in tree format
cat /proc/partitions           # Show partition info from kernel
sfdisk -l /dev/sdX             # List partitions
sgdisk -p /dev/sdX             # List GPT partitions
parted /dev/sdX print          # Show partition table
parted /dev/sdX unit MiB print # Show sizes in MiB
```

### Create and Manage Partitions
```bash
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

## 3. Remote Filesystems (NFS)

### Overview and Verification
```bash
# Server-side
exportfs -v                    # View current exports
systemctl status nfs-kernel-server # Check NFS server status

# Client-side
showmount -e server            # List exports on server
mount | grep nfs               # Show mounted NFS filesystems
df -h | grep nfs               # Show NFS filesystem usage
nfsstat                        # NFS statistics
rpcinfo -p server              # RPC services
```

### Setup and Management
```bash
# Server setup
apt install nfs-kernel-server  # Install NFS server
exportfs -ra                   # Refresh exports
systemctl start nfs-kernel-server
systemctl enable nfs-kernel-server

# Client setup
apt install nfs-common         # Install NFS client
mkdir -p /mnt/nfs              # Create mount point
mount -t nfs server:/shared/dir /mnt/nfs  # Mount NFS share
mount -t nfs -o ro,nosuid server:/shared/dir /mnt/nfs  # Mount with options
```

### Configuration Files
```bash
# Server configuration: /etc/exports
/shared/dir 192.168.1.0/24(rw,sync,no_subtree_check)
/backup    host.example.com(ro,sync,no_root_squash)

# Client configuration: /etc/fstab (for permanent mounts)
server:/shared/dir /mnt/nfs nfs defaults,_netdev 0 0
```

## 4. Swap Space

### Overview and Verification
```bash
free -h                        # Show memory and swap usage
swapon --show                  # Show swap devices
cat /proc/swaps                # Raw swap info from kernel
cat /proc/sys/vm/swappiness    # Current swappiness value
vmstat 1                       # Virtual memory statistics
```

### Create and Manage Swap
```bash
# Create swap partition
mkswap /dev/sdXY               # Format as swap
swapon /dev/sdXY               # Enable swap
swapoff /dev/sdXY              # Disable swap

# Create swap file
dd if=/dev/zero of=/swapfile bs=1M count=1024  # Create 1GB file
chmod 600 /swapfile            # Set permissions
mkswap /swapfile               # Format as swap
swapon /swapfile               # Enable swap

# Adjust swappiness (0-100, lower = less swapping)
sysctl vm.swappiness=10        # Temporary
echo "vm.swappiness=10" >> /etc/sysctl.conf  # Permanent
```

### Configuration Files
```bash
# /etc/fstab entries for permanent swap
/dev/sdXY none swap sw 0 0     # Swap partition
/swapfile none swap sw 0 0     # Swap file
```

## 5. Logical Volume Management (LVM)

### Overview and Verification
```bash
# Physical Volumes
pvs                            # List PVs (brief)
pvdisplay                      # List PVs (detailed)
pvscan                         # Scan for PVs

# Volume Groups
vgs                            # List VGs (brief)
vgdisplay                      # List VGs (detailed)
vgscan                         # Scan for VGs
vgdisplay vg_name -v | grep "Free PE"  # Show free space

# Logical Volumes
lvs                            # List LVs (brief)
lvdisplay                      # List LVs (detailed)
lvscan                         # Scan for LVs

# Overall view
lsblk | grep -E 'lvm|LVM'      # Show LVM in block devices
dmsetup ls                     # List device mapper devices
```

### Create and Manage LVM
```bash
# Physical Volumes (PV)
pvcreate /dev/sdX1 /dev/sdX2   # Create PVs
pvremove /dev/sdX1             # Remove PV

# Volume Groups (VG)
vgcreate vg_name /dev/sdX1 /dev/sdX2  # Create VG
vgextend vg_name /dev/sdX3     # Add PV to VG
vgreduce vg_name /dev/sdX1     # Remove PV from VG
vgremove vg_name               # Remove VG

# Logical Volumes (LV)
lvcreate -L 10G -n lv_name vg_name   # Create 10GB LV
lvcreate -l 80%VG -n lv_name vg_name # Use 80% of VG
lvextend -L +5G /dev/vg_name/lv_name  # Extend by 5GB
lvextend -l +100%FREE /dev/vg_name/lv_name # Use all free space
lvreduce -L -5G /dev/vg_name/lv_name  # Reduce by 5GB (unmount first!)
lvremove /dev/vg_name/lv_name  # Delete LV

# Filesystem Operations with LVM
resize2fs /dev/vg_name/lv_name # Resize ext4 filesystem after extending LV
xfs_growfs /mount/point        # Resize XFS filesystem (only grow, not shrink)

# Create/manage snapshots
lvcreate -L 1G -s -n snap_name /dev/vg_name/lv_name  # Create snapshot
mount -o ro /dev/vg_name/snap_name /mnt/snapshot     # Mount snapshot
lvremove /dev/vg_name/snap_name                      # Remove snapshot
```

### Configuration Files
```bash
# Main LVM configuration file
/etc/lvm/lvm.conf

# Device mapper files
/dev/mapper/
```
