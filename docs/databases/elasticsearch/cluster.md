## Health

```json
GET /_cluster/health
```


## Allocation Errors Explain

```json
GET /_cluster/allocation/explain
```

## Know Errors

### Max virtual memory (vm.max_map_count)

**max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]**

```bash
sudo sysctl -w vm.max_map_count=262144
```
