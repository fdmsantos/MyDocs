# Devices
 
* **Lists block devices**

```bash
lsblk

fdisk -l
```

## Create ext4 Filesystem

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

## Create LVM Filesystem

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
Partition number (1-3,5-7, default 7): 2           
Hex code (type L to list all codes): 8e

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): w
The partition table has been altered.
Syncing disks.

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
/dev/xvdf2       4196352 10487807 6291456    3G 8e Linux LVM
/dev/xvdf3      10487808 16777215 6289408    3G  5 Extended
/dev/xvdf5      10489856 12587007 2097152    1G 83 Linux
/dev/xvdf6      12589056 14686207 2097152    1G 83 Linux
/dev/xvdf7      14688256 16777215 2088960 1020M 83 Linux

Command (m for help): t
Partition number (1-3,5-7, default 7): 5            
Hex code (type L to list all codes): 8e

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): w
The partition table has been altered.
Syncing disks.

# pvcreate /dev/xvdf2 
  Physical volume "/dev/xvdf2" successfully created.
# pvcreate /dev/xvdf5
  Physical volume "/dev/xvdf5" successfully created.
# vgcreate VG1 /dev/xvdf2 /dev/xvdf5 
  Volume group "VG1" successfully created
root@ip-172-31-81-196:~# vgdisplay /dev/VG1
  --- Volume group ---
  VG Name               VG1
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               3.99 GiB
  PE Size               4.00 MiB
  Total PE              1022
  Alloc PE / Size       0 / 0   
  Free  PE / Size       1022 / 3.99 GiB
  VG UUID               ELopyA-2vai-XuiQ-vm7M-RF9q-k0ve-yUeXM6
  
# lvcreate VG1 -L +3.9G -n LV1
  Rounding up size to full physical extent 3.90 GiB
  Logical volume "LV1" created.
root@ip-172-31-81-196:~# lvdisplay
  --- Logical volume ---
  LV Path                /dev/VG1/LV1
  LV Name                LV1
  VG Name                VG1
  LV UUID                Q0mXgw-q9E7-ncuH-Vv2t-NTqx-epn9-3EK3DH
  LV Write Access        read/write
  LV Creation host, time ip-172-31-81-196, 2019-08-02 21:45:49 +0000
  LV Status              available
  # open                 0
  LV Size                3.90 GiB
  Current LE             999
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
# mkfs.ext4 /dev/VG1/LV1
mke2fs 1.44.1 (24-Mar-2018)
Creating filesystem with 1022976 4k blocks and 256000 inodes
Filesystem UUID: 3cc6d97e-a315-4051-a572-16e06bf9c9f9
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done 

root@ip-172-31-81-196:~# mkdir /mnt/LV1-mount 
root@ip-172-31-81-196:~# mount /dev/VG1/LV1 /mnt/LV1-mount
root@ip-172-31-81-196:~# vi /etc/fstab 
/dev/VG1/LV1 /mnt/LV1-mount ext4 defaults 0 0 # Add This Line
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

