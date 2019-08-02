# Devices
 
* **Lists block devices**

```bash
lsblk

fdisk -l
```

## Create Partition And Mount FileSystem

**Can use parted command**

```sh
# lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0     7:0    0 88.5M  1 loop /snap/core/7270
loop1     7:1    0   18M  1 loop /snap/amazon-ssm-agent/1455
xvda    202:0    0    8G  0 disk 
└─xvda1 202:1    0    8G  0 part /
xvdf    202:80   0    8G  0 disk 

# fdisk /dev/xvdf
Command (m for help): n  => Create Partition
Partition type:
  p   primary (0 primary, 0 extended, 4 free)
  e   extended
Command (m for help): p   => Print partition
Command (m for help): w   => Write

# mkfs -L projectA -t ext4 /dev/xvdf1  # Format Partition
# mkdir /mnt/ProjectA
# mount /dev/xvdf1 /mnt/ProjectA/ 
# vi /etc/fstab
LABEL=projectA /mnt/ProjectA/ ext4 defaults 0 0 # Add This Line
```

```bash
umount /mnt/ProjectA/ 
```

## Create Swap Partition

**Can use parted command**


```sh
# fdisk /dev/xvdf

Welcome to fdisk (util-linux 2.31.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/xvdf: 8 GiB, 8589934592 bytes, 16777216 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xaef5270b

Device     Boot    Start      End Sectors  Size Id Type
/dev/xvdf1          2048  4196351 4194304    2G 82 Linux swap / Solaris
/dev/xvdf2       4196352 10487807 6291456    3G 83 Linux
/dev/xvdf3      10487808 16777215 6289408    3G  5 Extended
/dev/xvdf5      10489856 12587007 2097152    1G 83 Linux
/dev/xvdf6      12589056 14686207 2097152    1G 83 Linux
/dev/xvdf7      14688256 16777215 2088960 1020M 83 Linux

Command (m for help): t
Partition number (1-3,5-7, default 7): 1
Hex code (type L to list all codes): 82
Command (m for help): w

# mkswap /dev/xvdf1
# swapon /dev/xvdf1 # to shutoff -> swapoff /dev/xvdf2
# free -m

# vi /etc/fstab 
UUID=99fd52d5-e821-45a5-9366-30666046406f swap swap default 0 0
```

# FileSystem


|  Dir  |      Function      |   Extras    |
| -------- |:-------------:| ---------:|
| /dev | (udev) Device manager. Contain device files | Config in /etc/udev |
| /sys | (sysfs) Virtual file system. Info about Hardware devices, drivers |  |
| /proc | (procfs)Similiar to sysfs, but with info about processes and system info | Can be used to interface with the kernel. Change parameters on the fly  |

* **File System space usage**

```bash
df -h
```

