# Kernel Modules

```sh
# To view who use the modules
lsmod

# To remove
sudo rmmod video

# To enable
sudo modprobe video
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

# Hardware

## PCI Devices

```sh
# To list pci Devices
lspci
lspci -v
lspci -vvv
```

