## Monitoring

* BinLogDiskUsage 
    * Amount of disk space occupied by binary logs on the primary database. Applies to MySQL Read Replicas and is measured in bytes
* DatabaseConnections
    * Number of database connections in use
* DiskQueueDepth
    * The number of outstanding read and write requests waiting to access the disk
* FreeStorageSpace 
    * Amount of available storage space
* FreeableMemory 
    * Amount of available RAM
* NetworkReceiveThroughput 
    * Inbound network traffic on the DB instance that includes both customer database traffic and Amazon RDS traffic used for monitoring and replication
* NetworkTransmitThroughput 
    * Outbound network traffic on the DB instance that includes both customer database traffic and Amazon RDS traffic used for monitoring and replication
* ReadIOPS/WriteIOPS
    * Average number of disk I/O operations per second
    * Use this to determine storage type changes
* ReadLatency /WriteLatency
    * Average amount of time taken per disk I/O operation
    * More IOPS needed
* ReadThroughput/WriteThroughput
    * Average number of bytes read from disk per second
* ReplicaLag
    * Amount of time a Read Replica DB instance lags behind the source DB instance. Applies to MySQL, MariaDB, and PostgreSQL Read Replicas
* SwapUsage 
    * Amount of swap space used on the DB instance
    * If increase - low or no available ram
* High CPU or RAM consumption High values for CPU or RAM consumption might be appropriate, provided that they are within an expected range.
* Disk space consumption Investigate disk space consumption if there is
consistently less than 15 percent of available free space
* Network traffic Investigate network traffic if throughput is consistently lower
than expected
* Database connections If an increase of usage causes performance issues, consider limiting the number of available database connections. The best number of user connections for a DB instance will depend on the instance class and the complexity of the operations performed.
* IOPS metrics The expected values for IOPS metrics depend on disk specification and server configuration. Establish a baseline to know what utilization is typical. For best IOPS performance, make sure that the typical working set will fit into memory to minimize read and write operations.

* Aurora 100% CPU utilization
    * Is it writes causing the issue? if so scale up (increase instance size)
    * Is it reads causing the issue? If so scale out (increase the number of read replicas)
