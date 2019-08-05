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
