# ECS

## Windows

### Troubleshoot Webserver

```bash
docker exec -it container_id powershell
Get-EventLog Application -Newest 10 | select -ExpandProperty message
```