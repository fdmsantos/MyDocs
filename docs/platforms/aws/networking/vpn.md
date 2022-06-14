# VPN

## Site-to-Site

### Amazon Linux 2 Openswan

```shell script
$ sudo yum install openswan -y
$ cat /etc/sysctl.conf
net.ipv4.ip_forward = 1
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0

$ sudo service network restart
# Follow AWS Configuration file
$ sudo systemctl start ipsec****
```