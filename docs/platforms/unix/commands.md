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

* Start

```bash
systemctl start kubelet.service
systemctl status kubelet.service
```

* Journalctl

```bash
journalctl -u kubelet.service
```

## Networking

* Netstat

```bash
$ sudo netstat -nl -p tcp  | grep 8123
$ sudo netstat -nl -p tcp  | head
```

* SS

```bash
ss -an | grep -i listen
```

* Who listen on Port

```bash
lsof -i :8000
```

* Test Remote Connection to port

```bash
nc -v 10.240.100.18  2049
```

## Find

```bash
sudo find / -type f -name .gitconfig
```

## SSH

* Permit SSH Root Login (CentOs)

```sh
vi /etc/ssh/sshd_config
# Comment PasswordAuthentication no
# Descomment PasswordAuthentication yes
systemctl restart sshd 
```

## Configure Network Interface (CentOs)

```sh
vi /etc/sysconfig/network-scripts/ifcfg-eth0
IPADDR=172.16.1.180
GATEWAY=172.16.1.16
DNS1=172.16.1.112
DNS2=172.16.1.119
systemctl restart network
```