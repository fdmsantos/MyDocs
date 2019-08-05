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

# Commands

## Dirs

### Create

* **Multiple Dirs**

```bash
mkdir -p {networking,compute,storage}
```

* **Multiple Files in Multiple Dirs**

```bash
touch {networking,compute,storage}/{main.tf,variables.tf,outputs.tf}
```

## Find

```bash
sudo find / -type f -name .gitconfig
```

## Inodes

* **Get File Inode**

```bash
ls -i /etc/passwd
```

* **Find File by Inode**

```sh
find / -inum 55116 /etc/passwd
```

* Get Inodes Available

```bash
df -i
```

## Disk Space

```sh
du -h

du -s # To sum the total

du -s /

du -h --max-depth=1 # Show the disk usage by folder, and not the sub dirs because of Depth = 1
```

## Filesystem

### fsck (filesystem check)

* **To check errors from filesystem**
* **Don't do in mounted filesystem**

```bash
sudo fsck /dev/sdb1
sudo umount /dev/sdb1
```

### dumpe2fs

* **Get info about filesystem**

```sh
sudo dumpe2fs -h /dev/sdb1
```

### tune2fs

* **To get info and tune filesystem**

```sh
tune2fs -l /dev/xvda1
```

* **Set Volume Name**

```sh
tune2fs -L Photos /dev/xvda1
```

* **Set mount counts will be filesystem checked**

```sh
tune2fs -c 10 /dev/xvda1
```

### fuser

* **Show which process is using the directory**

```sh
# fuser /mount/Photos
/mount/Photos:              3157c

# ps aux | grep 3157
-- Will see the process, probably /bin/bash

# ps auxf  -> Show which command executed and the login session called
```

## Disk Quotas

```bash
# vi /etc/fstab
LABEL=projectA /mnt/ProjectA/ ext4 defaults,usrquota 0 0 
# mount -a
# apt-get install quota
# cd /mnt/ProjectA
# quotacheck -avugc # Create filesystem to support quotas
quotacheck: Your kernel probably supports journaled quota but you are not using it. Consider switching to journaled quota to avoid running quotacheck after an unclean shutdown.
quotacheck: Scanning /dev/xvdf7 [/mnt/ProjectA] done
quotacheck: Cannot stat old user quota file /mnt/ProjectA/aquota.user: No such file or directory. Usage will not be subtracted.
quotacheck: Old group file name could not been determined. Usage will not be subtracted.
quotacheck: Checked 3 directories and 0 files
quotacheck: Old file not found.
# edquota -u fsantos # To edit quotas to user
# quotaon /mnt/Photos # Enable quotas
# quota -v # To see quotas usage
# sudo repquota /mnt/Photos # To see a resume from users

```