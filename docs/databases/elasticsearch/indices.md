# Indices

## Open

```json
POST /{index}/_open
```

## Close

```json
POST /_all/_close
```

## Know Errors

### Forbidden Index x-read-only-allow-delete-api

[Forbidden Issue](https://discuss.elastic.co/t/forbidden-12-index-read-only-allow-delete-api/110282)

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