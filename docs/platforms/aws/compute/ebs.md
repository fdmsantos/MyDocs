## Resize Root Volumes

* **Manual method**

    1. Create a snapshot of the current root volume
    2. Create a new volume from the snapshot with new storage specifications
    3. Select larger "size", thus increasing IOPS
    4. Must select the same availability zone as the source
    5. Stop the Instance
    6. Detach the original root volume
    7. Attach the new volume to the instance at same mount point (xvda)

* **"Automated" method**
    1. We can replace the launch configuration of an Auto Scaling Group
    2. In a true 3-tier application that is decoupled, we should then be able to terminate instances one by one to recreate them using new configuration

**Note:** If the size of the volume was modified, extend the volume's file system to take advantage of the increased storage capacity

## Monitoring

### Metrics

* VolumeReadOps
    * Total number of IOPS in a specific period of time. To 1000 in 1 minute = 1000/60 IOPS - Change IOPS based off of usage.
* VolumeWriteOps
    * Total number of IOPS in a specific period of time . TO 1000 in 1 minute = 1000/60 iops - Change IOPS based off of usage
* VolumeQueueLength
    * The number of read/write operation requests waiting to process. We want close at 0. If higher than 0, increase the number of available IOPS
* VolumeReadBytes
* VolumeWriteBytes
* VolumeTotalReadTime
* VolumeTotalWriteTime
* VolumeIdleTime
* VolumeThroughputPercentage
    * Provisioned IOPS SSD only
    *Determine the percentage of I/O operations delivered of the total IOPS provisioned for an EBS volume. During a write, it there are no other pending I/O requests in a minute, the metric value will be 100 percent.
* VolumeConsumedReadWriteOps
    * Provisioned IOPS only
* BurstBalance 
    * (gp2, st1, and sc1 volumes only)
* Reach IOPS limit of your volume will result in:
    * Will start to get your IO requests queuing
    * Depending on your applications sensitivity to IOPS and latency, you may see your applications becaming slow
    
### Totals IOPS / Total Throughput / Total Network 

[StackOverflow](https://stackoverflow.com/questions/60900782/cloudwatch-metrics-for-volume-iops-volume-throughput-mib-s-and-network-gbps)

### Key Metrics

[Key Metrics](https://www.datadoghq.com/blog/amazon-ebs-monitoring/)

### Anomaly Detection

[Anomaly Detection](https://www.bluematador.com/blog/how-to-monitor-amazon-ebs-with-cloudwatch)
