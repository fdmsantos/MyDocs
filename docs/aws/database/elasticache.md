# Monitoring
 
## CPU Utilization

* **MemCached**
    * Multi-threaded
     * Can handle loads of up to 90%. if it exceeds 90% Increase the node family or add more nodes to cluster or read replicas

* **Redis**
    * Not multi-threaded. To determine the point in which to scale, take 90 and divide by the number of cores
    * Example: if you have 4 cpus, the threshold would be ( 90 / 4 = 22.5%) 

* if this threshold is exceeded and the main workload is from read requests, scale the cache cluster out by adding read replicas. If the main workload is from write requests, AWS recommends scaling up by using a larger cache instance type
 

## Evictions
 
* An Eviction occurs when a new item is added and an old item must be removed due to lack of free space in the system 
* Older items are removed to free up memory for new items 
* Frequent evictions will decrease performance - Increase the node size (memory)
* No recommended setting. Choose the threshold based off of application requirements
* **Memcached**
    * Scale UP ( increase the memory of existing nodes) OR
    * Scale OUT ( add more nodes)
* **Redis**
    * Scale Out ( add more read replicas)

## Swap Usage

* Swap usage is simply the amount of the Swap file that is used. Swap file (or paging file) is the amount of disk storage space reserved on disk if your computer runs out of ram. Typically the size of swap file = the size of the RAM. So if you have 4GB of RAM, you will have 4GB SWAP file
* **Memcached** 
    * Affects performance if increased 
    * Should be close to 0 (should not exceed 50mb) 
    * If this exceeds 50 Mb you should increase the memcached_connections_overhead parameter
    * Defines the amount of memory to be reserved for connections and other misc/overhead. 
* **Redis** 
    * AWS suggest not setting a cloudwatch alarm as there is no suggested "web service" fix
    * No SwapUsage metric, instead use reserved-memory
 
## CurrConnections
 
* **MemCached & Redis**
    * No recommended setting, Chooses threshold based off of application requirements
    * Increased CurrConnections could indicate a larger issue with your application OR the ELB may not be releasing connections (tied down) or is a large and sustained spike in the number of concurrent connections
    * Remember to set an alarm on the number of concurrent connections for elasticcache