# Syslog

## Config Files

```bash
cd /etc/rsyslog.d/
```

## Rotate Files Conf

```bash
vi /etc/logrotate.conf
```

# Journalctl

* **Log For Services

```bash
journalctl # All
journalctl -b # Last Boot
journalctl -b 1 # Before Last Boot
journalctl --since "2 days ago"
journalctl -u kubelet.service
vi /etc/system.d/journald.conf # Config Files
```
