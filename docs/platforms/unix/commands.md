# Commands

## SSL Certificates

### Verify

```bash
openssl verify cert.pem
openssl verify -untrusted ca-bundle cert.pem
```

### Certbot

[WildCard Certificates](https://medium.com/@saurabh6790/generate-wildcard-ssl-certificate-using-lets-encrypt-certbot-273e432794d7)

[AMI2 Certificates Automation](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html)

[Issue - No module named cryptography](https://stackoverflow.com/questions/57891591/how-to-fix-importerror-no-module-named-cryptography)

## Folders

* Create Multiple Folders

```bash
mkdir -p {networking,compute,storage}
```

* Create Multiple Files in Multiple Folders

```bash
touch {networking,compute,storage}/{main.tf,variables.tf,outputs.tf}
```

## Services

### Journalctl

```bash
journalctl -u kubelet.service
journalctl # All
journalctl -b # Last Boot
journalctl -b 1 # Before Last Boot
journalctl --since "2 days ago"
vi /etc/system.d/journald.conf # Config Files
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

## Tar

```bash
# List files without extract
tar -tvf etcd-v3.3.13-linux-amd64.tar.gz

# Extract only one file
tar -xvf etcd-v3.3.13-linux-amd64.tar.gz etcd-v3.3.13-linux-amd64/etcdctl
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

## Kernel Modules

```sh
# To view who use the modules
lsmod

# To remove
sudo rmmod video

# To enable
sudo modprobe video
```

## Libs

```bash
# Get Shared libs location
cat /etc/ld.so.conf

# To load new lib
ldconfig

# Change Library Path temporary
export LD_LIBRARY_PATH=/home/nick/lib

# Print shared libs from app
ldd /bin/ls
```

## Hardware

* List pci Devices

```sh
lspci
lspci -v
lspci -vvv
```

## Syslog

* Config Files

```bash
cd /etc/rsyslog.d/
```

* Rotate Files Conf

```bash
vi /etc/logrotate.conf
```
