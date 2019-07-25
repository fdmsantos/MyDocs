## Recover Root Password

* Na console

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

## Permit SSH Root Login

```sh
vi /etc/ssh/sshd_config
# Comment PasswordAuthentication no
# Descomment PasswordAuthentication yes
systemctl restart sshd 
```

## Configure Network Interface

```sh
vi /etc/sysconfig/network-scripts/ifcfg-eth0
IPADDR=172.16.1.180
GATEWAY=172.16.1.16
DNS1=172.16.1.112
DNS2=172.16.1.119
systemctl restart network
```
