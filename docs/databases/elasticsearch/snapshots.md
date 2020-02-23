# Snapshots

## Repositories

#### Create

```json
PUT /_snapshot/my_backup
{
    "type": "fs",
    "settings": {
      "location": "/usr/share/elasticsearch/snapshots/backup"
    }
  
}
```

#### List

```json
GET /_cat/repositories?v
GET /_snapshot/_all
```

#### Delete

```json
DELETE /_snapshot/{repository}
```


## Snapshots

* Elasticsearch config

**Change parameter in elastic search config file for all nodes**

The path.repo needs to be a shared folder beetween the cluster

```yaml
path.repo: ["/usr/share/elasticsearch/snapshots"]
```

### Create

```json
PUT /_snapshot/{repository}/{snapshot}?wait_for_completion=true
```

### List all Snapshots from a repo

```json
GET /_cat/snapshots/{repository}?v&s=id
```

### Delete

```json
DELETE /_snapshot/{repository}/{snapshot}
```


## Restore

[Restore - Official Doc](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html#_restore)

### Restore From one Snapshot

```json
POST /_snapshot/{repository}/{snapshot}/_restore
```

