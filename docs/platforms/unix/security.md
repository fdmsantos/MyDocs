# Security

## Host

```bash
cat /etc/hosts.allow # Hosts allowed to access

cat /etc/hosts.deny # Hosts not allowed to access
```

## Nologin

```bash
touch /etc/nologin # Denies login to all users (except root). Need remove the file
```

## SE Linux

### Ubuntu

```bash
# Need remove apparmor in ubuntu systems
# make sure you have the most up-to-date info
apt-get update
apt-get dist-upgrade

#disable and remove apparmor
/etc/init.d/apparmor stop
apt-get remove apparmor

#install SELinux
apt-get install selinux

# install the missing dependency
apt-get install auditd

# install the activate tool required to make it work
apt-get install selinux-basics

#missing manual step to actually make SELinux work (part of selinux-basics)
selinux-activate
```

### Config

```bash
# Config file
cat /etc/selinux/config
getenforce
setenforce 1
sestatus
ls -Z /etc/shadow
ps -Z

```

### Contexts

```bash
# List
semanage fcontext -l
semanage fcontext -l | grep httpd_sys_content_t
# Change Context Type
chcon -t user_home_dir_t /etc/shadow
```

### Booleans

```bash
getsebool -a
semanage boolean -l | sort | less
semanage boolean -m -1 httpd_enable_homedirs # or setsebool -P httpd_enable_homedirs=1
```

### Troubleshooting

```bash
grep http /var/log/audit/audit.log
```