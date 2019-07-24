## Deploy

### Docker-compose

```sh
git clone https://github.com/maxyermayank/docker-compose-elasticsearch-kibana
```

## Know Errors


### Max virtual memory

**Desc:** Max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]

```sh
sudo sysctl -w vm.max_map_count=262144
```


### Forbidden Index x-read-only-allow-delete-api

[Link](https://discuss.elastic.co/t/forbidden-12-index-read-only-allow-delete-api/110282)

```json
PUT .kibana/_settings
{
    "index": {
      "blocks": {
        "read_only_allow_delete": "false"
      }
    }
}
```