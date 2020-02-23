## Recover Root Password

**Na console**

```sh
send ctrl-alt-del
press "e" => TO Edit
Na linha linux16 -- No final da linha adicionar rd.break
press "ctrl-x"
mount -o remount,rw /sysroot
cd /sysroot
chroot .
touch .autorelabel
passwd teste123ibm4
ctrl d
ctrl d
```