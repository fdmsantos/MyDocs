title: Kibana

# Kibana

## Time Series

#### Filters

```sh
performancemanager.virtualmachines.metric.info.metric: "cpu.usagemhz.average" AND NOT performancemanager.virtualmachines.metric.sample.instance: "*" AND performancemanager.hosts.metric.sample.instance: "*"
```

## Timelion

#### Expression

```sh
.es(index=vspherebeat-emp-imopolis-*, timefield=performancemanager.datastoresclusters.metric.sample.timestamp, metric=sum:performancemanager.datastoresclusters.metric.sample.value,q="performancemanager.datastoresclusters.metric.info.metric: disk.provisioned.latest").divide(1000000000).label("Disk Provisioned [TB]").color(black).lines(fill=1,width=2).title("Capacity Assessment"),

.es(index=vspherebeat-emp-imopolis-*, timefield=performancemanager.datastoresclusters.metric.sample.timestamp, metric=sum:performancemanager.datastoresclusters.metaData.Storage.Capacity,q="performancemanager.datastoresclusters.metric.info.metric: disk.provisioned.latest").divide(1000000000000).label("Total Capacity [TB]").color(yellow).lines(fill=2,width=2),

.es(index=vspherebeat-emp-imopolis-*, timefield=performancemanager.datastoresclusters.metric.sample.timestamp, metric=sum:performancemanager.datastoresclusters.metric.sample.value,q="performancemanager.datastoresclusters.metric.info.metric: disk.used.latest").divide(1000000000).label("Disk Used [TB]").color(green).lines(fill=3,width=1),

.es(index=vspherebeat-emp-imopolis-*, timefield=performancemanager.datastoresclusters.metric.sample.timestamp, metric=sum:performancemanager.datastoresclusters.metaData.Storage.Capacity,q="performancemanager.datastoresclusters.metric.info.metric: disk.provisioned.latest").divide(1000000000000).multiply(1.1).label("Provisioning Threshold [TB]").color(red).lines(fill=0,width=3),
```