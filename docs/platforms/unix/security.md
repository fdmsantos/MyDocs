# Security

## FirewallD

```bash
# Logs
/var/log/secure

# Get State
sudo firewall-cmd --state

# list available Zones
sudo firewall-cmd --get-zones

# List zones used by network interfaces
sudo firewall-cmd --get-active-zones

# Get Default Zone
sudo firewall-cmd --get-default-zone

# Get Zone configuration Settings
sudo firewall-cmd --zone=public --list-all

# Open Port
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
sudo firewall-cmd --reload

# Close Port
sudo firewall-cmd --zone=public --remove-port=80/tcp --permanent
sudo firewall-cmd --reload

# Firewall services -XMl service Files
sudo cd /usr/lib/firewalld/services/

# Add Service
sudo firewall-cmd --permanent --zone=public --add-service=myHttp
sudo firewall-cmd --reload

# Remove Services
sudo firewall-cmd --permanent --zone=public --remove-service=myHttp
sudo firewall-cmd --reload

# Allow traffic from one IP to a port
sudo firewall-cmd --permanent --zone-public --add-rich-rule='
    rule family ="ipv4"
    source address="<IP or Network>"
    port protocol="<PROTOCOL>" port=">PORT_NUMBER>" accept'

# Allow traffic from a list of IPS (Whitelist)
sudo firewall-cmd --permanent --zone=public --add-source=<IP or NETWORK>

# Blacklist
sudo firewall-cmd --permanent --new-ipset=blacklist --type=hash:ip # Create IpSet
sudo firewall-cmd --get-ipsets
sudo firewall-cmd --info-ipset=blacklist
sudo firewall-cmd --ipset=blacklist --add-entry=222.186.15.114 # Add Entry IpSet
sudo firewall-cmd --ipset=blacklist --get-entries
sudo firewall-cmd --permanent --zone=drop --add-source=ipset:blacklist # Apply IpSet
sudo firewall-cmd --permanent --ipset=<SET_NAME> --add-entries-from-file=<FILE_NAME> # Add Entries From File
sudo firewall-cmd --permanent --ipset=<SET_NAME> --remove-entries-from-file=<FILE_NAME> # Remove Entries From file

# Forward traffic
sudo firewall-cmd --zone=<ZONE_NAME> --add-forward-port=port=<TO_PORT>:proto=tcp:toaddr=<IP>

# LockDown - Restrict Apps change Firewalld
sudo firewall-cmd --query-lockdown # Check if is in lockdown mode
sudo vi /etc/firewalld/firewalld.conf # Apply Lock lockdown mode
    Lockdown=yes
sudo systemctl restart firewalld

# Panic  - Restrict traffic (Yourself too) - Extreme, Need access via console to recover
sudo firewall-cmd --query-panic
sudo firewall-cmd --panic-on
sudo firewall-cmd --panic-off
```

## Block Ping

```bash
sudo vim /etc/sysctl.conf
    net.ipv4.icmp.echo_ignore_all = 1
# Apply changes
sudo sysctl -p
```

## SE Linux

### States

```bash
sudo getenforce # Get current state
sudo sestatus
sudo setenforce 0 # Run-Time state change to permissive
sudo setenforce 1 # Run-Time state change to Enforcing
Vi /etc/selinux/config # Permanently Disable
    SELINUUX = disabled
# Reboot the system
```

### Contexts

* Operations
    * Move - Moved files will retain their current context
    * Copy - Copied files might not retain their current content
    * Create - Created files will inherit the context for the location where they are created
* Context
    * system_u:object_r:httpd_sys_content_t
    * system_u = User Context
        * system_u - This is for system users
        * user_u - This is for your average user that logs into the Linux machine
        * root - This will limit to root-user-only access
    * object_r = role context
        * object_r - Generally used for process and domains for files
    * httpd_sys_content_t = Type Content
        * This Allows us to have an easy way of fine tuning control

```bash
ls -Z # To see files and the contexts
ps -eZ # To see proccess and the contexts
semanage fcontext -l # List SE Contexts

# Change Context
sudo semanage fcontext -a -t httpd_sys_content_t /var/www/html/index.html
sudo restorecon -v /var/www/html/index.html

# Remove Context
sudo semanage fcontext -d /var/www/html/index.html
sudo restorecon -v /var/www/html/index.html

# Change Context Type -> Temporary
chcon -t user_home_dir_t /etc/shadow

# Checks if the default SElinux context is set
matchpathconf -V <path to file>
```

### Booleans

```bash
getsebool -a
# List booleans
semanage boolean -l | sort | less
# Set boolean, temporary change
setsebool <boolean> on/off
# Set boolean, permanent change
setsebool -P <boolean> on/off

```

### Ports

* The Rules will apply in the initial binding. if you change httpd port, you need restart

```bash
# List Ports
sudo semanage port -l | grep http
# Add Ports or Protocol to label
sudo semanage port -a -t http_port_t -p tcp 61613
sudo semanage port -m -t http_port_t -p tcp 61613 # If the port is already in use
# Remove port or protocol
sudo semanage port -d -t http_port_t -p tcp 61613
```

### Domains

```bash
# Get Domain
ps -eZ | grep httpd
# Configure a domain to run in permissive mode
sudo semanage permissive -a httpd_t
# Remove permissive mode on a domain
sudo semanage permissive -d httpd_t
# Disable permissive mode across all domains
semodule -d permissivedomains
```

### Modules

```bash
# List SELinux Modules
sudo semodule -l

# Generate policy module based on the log entries
sudo grep test.html /var/log/audit/audit.log | audit2allow -M  myModule
# Load Module
semodule -i <FILE NAME>
# Disable module
semodule -d <MODULE NAME>
```

### Logs

```bash
#log File
/var/log/audit/audit.log

# Install Tools
sudo yum install -y setroubleshoot setools

# Analyze logs
sudo grep httpd /var/log/audit/audit.log | audit2why

sudo ausearch -m USER_LOGIN -sv no

# Check for user activity
sudo ausearch -ua <USERNAME> -ts yesterday -te now

```

## Host

```bash
cat /etc/hosts.allow # Hosts allowed to access

cat /etc/hosts.deny # Hosts not allowed to access
```

## Nologin

```bash
touch /etc/nologin # Denies login to all users (except root). Need remove the file
```

### Troubleshooting

```bash
grep http /var/log/audit/audit.log
```