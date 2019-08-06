# Config

## ifconfig

```bash
ip addr
ifconfig
ifconfig -a
ifdown enp0s3
ifup enp0s3
ifconfig enp0s3 192.168.0.150/24 # Not Permanent
```

### Add Secondary IP

[Link](https://www.garron.me/en/linux/add-secondary-ip-linux.html)

```bash
ifconfig eth0:1 172.31.81.196 netmask 255.255.240.0 up # Not Permanent
```

## ip

```bash
ip a 
ip addr show
```

### Remove Secondary Ip

```bash
ip addr del 172.31.81.196/20 dev eth0:1 # Not Permanent
```

## Network-Manager

```bash
sudo apt install network-manager
sudo service network-manager restart
nmcli
nmcli connection # Connection information
# Add Static Route
sudo nmcli connection add con-name STATIC ipv4.addresses 192.168.58.1/24 ifname eth0 type ethernet
nmcli connection show STATIC
sudo nmcli connection modify  STATIC +ipv4.routes "172.16.0.0/16 192.168.58.254" ipv4.dns 172.16.58.254
```

## route

```bash
route
route -n
route add default gw 192.168.0.254
```

## Dns

```bash
cat /etc/hosts
cat /etc/nsswitch.conf # show how priority to will resolve dns

cat /etc/resolv.conf
cat /etc/dhcp/dhclient.conf

sudo service networking restart
```

## File

```bash
cat /etc/network/interfaces
```

# Connectivity

## Netstat

```bash
$ sudo netstat -nl -p tcp  | grep 8123
$ sudo netstat -nl -p tcp  | head
```

## Get open ports

```bash
nmap localhost
netstat -at

```

## SS

```bash
ss -an | grep -i listen
```

## Who listen on Port

```bash
sudo lsof -i :8000
```

## Test Remote Connection to port

```bash
nc -v 10.240.100.18  2049

netcat -l 12345 # To listen a Port
```

## Get Ports than services are listen

```bash
cat /etc/services
```

## MTR

```bash
mtr acloud.com
```

## DNS

```bash
host google.com
dig google.com
```