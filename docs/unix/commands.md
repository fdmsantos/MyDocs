# Networking

## Netstat

```bash
$ sudo netstat -nl -p tcp  | grep 8123
$ sudo netstat -nl -p tcp  | head
```

## SS

```bash
ss -an | grep -i listen
```

## Who listen on Port

```bash
lsof -i :8000
```

## Test Remote Connection to port

```bash
nc -v 10.240.100.18  2049
```

# Services

## Start

```bash
systemctl start kubelet.service
systemctl status kubelet.service
```

## Journalctl

```bash
journalctl -u kubelet.service
```

# Kernel Modules

```sh
# To view who use the modules
lsmod

# To remove
sudo rmmod video

# To enable
sudo modprobe video
```

# Hardware

## PCI Devices

```sh
# To list pci Devices
lspci
lspci -v
lspci -vvv
```

# Libs

## Shared

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

# Dirs

## Create Multiple Dirs

```bash
mkdir -p {networking,compute,storage}
```

## Create Multiple Files in Multiple Dirs

```bash
touch {networking,compute,storage}/{main.tf,variables.tf,outputs.tf}
```

# Find

```bash
sudo find / -type f -name .gitconfig
```