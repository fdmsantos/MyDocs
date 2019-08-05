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