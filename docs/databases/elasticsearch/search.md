# Search

## All

```json
GET /vspherebeat/_search
{
    "query": {
        "match_all": {}
    }
}
```

## Field Match string

```json
GET /vspherebeat/_search
{
    "query": {
        "match" : {
            "performancemanager.hosts.metaData.name": "nsvwsdv001.mngt.local"
        }
    }
}

```

## Field doesn't match string

```json
GET /vspherebeat/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "performancemanager.virtualmachines.metaData.name": "WSTPMNGT007" 
          }
        }
      ]
    }
  }
}

```

## Bool Query (Match and Not Match)

```json
GET /vspherebeat/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "performancemanager.virtualmachines.metaData.name": "APM-Server"
          }
        },
        {
          "match": {
            "performancemanager.virtualmachines.metric.info.metric": "cpu.usage.average" 
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "performancemanager.virtualmachines.metric.sample.instance": "*"
          }
        }
      ]
    }
  }
}
```

## Exists Specified Field

```json
GET /vspherebeat/_search
{
  "query": {
    "exists": {
      "field": "performancemanager.hosts"
    }
  }
}
```

## Unique Values from a field

```json
GET vspherebeat/_search
{
  "size":"0",
  "aggs" : {
    "uniq_hotsr" : {
      "terms" : { "field" : "performancemanager.hosts.metaData.name" }
      }
  }
}
```

## Total of unique values from a field

```json
GET /vspherebeat/_search
{
    "size" : 0,
    "aggs" : {
        "distinct_hots" : {
            "cardinality" : {
              "field" :  "performancemanager.hosts.metaData.name"
            }
        }
    }
}
```
 

