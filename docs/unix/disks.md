## Mount Disk

### List Devices

```sh
fdisk -l
```

### Create Partition

```sh
# fdisk /dev/sdb
Command (m for help): n  => Create Partition
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Command (m for help): p   => Get partition
Command (m for help): w   => Write 
```

### Format and Create Label

```sh
# mkfs.xfs /dev/sdb1 -L elastic
```

### Mount

```sh hl_lines="2"
# vi /etc/fstab
LABEL=elastic /elastic                          xfs     defaults        0 0  ## Add this line
# mount -a
```

## Mount Share Disk

### Install NFS

```sh
yum install -y nfs-utils
systemctl enable nfs
systemctl start nfs 
```

### Master

#### Configure Export

```sh hl_lines="2"
# vi /etc/exports
/snapshots *(rw)  => Add Line
# exportfs
/snapshots      <world>
```

#### Enable Service

```sh
firewall-cmd --add-service nfs --permanent
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --permanent --add-service=mountd
firewall-cmd --permanent --add-port=2049/tcp
firewall-cmd --permanent --add-port=2049/udp
firewall-cmd --reload
```

### Slaves

```sh hl_lines="2"
# vi /etc/fstab 
10.240.100.18:/snapshots /elastic/snapshots     nfs     _netdev,rw      0 0
# mount -a
# mount | grep elastic
```
