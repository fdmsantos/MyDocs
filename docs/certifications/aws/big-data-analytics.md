# Collection (Not in Mind Map)

## 3 Ways move data to AWS

* Real Time - Immediate Actions
  * Kinesis Data Streams
  * SQS
  * IOT

* Near Real-Time - Reactive Actions
  * Kinesis Data Firehose
  * DMS

* Batch - Historical Analysis
  * Snowball
  * Data Pipeline

# S3  (Not in Mind Map)

* Single Objects can be up to 5TB
* Single Put Operation can upload 5 GB (Multipart upload)
* HIPAA Compliant
* Glacier
  * S3 Glacier Instant Retrievel
    * Milisecond retrievel, great for data acessed once a quarter
    * Minimum storage duration of 90 days
  * S3 Glacier Flexible Retrievel
    * Expedited (1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours) - free
    * Minimum storage duration 90 days
  * S3 Glacier Deep Darchive - Long term storage
    * Standard (12 Hours), Bulk (48 hours)
    * Minimum storage duration 180 days

* Lifecycle Rules
  * Transition actions: It defines when objects are transitioned to another storage class.
    • Move objects to Standard IA class 60 days after creation
    • Move to Glacier for archiving after 6 months
  • Expiration actions: configure objects to expire (delete) after some time
     • Access log files can be set to delete after a 365 days
     • Can be used to delete old versions of files (if versioning is enabled)
     • Can be used to delete incomplete multi-part uploads
  • Rules can be created for a certain prefix (ex - s3://mybucket/mp3/*)
  • Rules can be created for certain objects tags (ex - Department: Finance)
* S3 Replication (CRR & SRR)
  • Must enable versioning in source and destination
  • Cross Region Replication (CRR)
  • Same Region Replication (SRR)
  • Buckets can be in different accounts
  • Copying is asynchronous
  • Must give proper IAM permissions to S3
  • CRR - Use cases: compliance, lower latency access, replication across accounts
  • SRR – Use cases: log aggregation, live replication between production and test accounts

  • After activating, only new objects are replicated (not retroactive)
  • For DELETE operations:
  • Can replicate delete markers from source to target (optional setting)
  • Deletions with a version ID are not replicated (to avoid malicious deletes)
  • There is no “chaining” of replication
  • If bucket 1 has replication into bucket 2, which has replication into bucket 3
  • Then objects created in bucket 1 are not replicated to bucket 3

* Performance
  • Amazon S3 automatically scales to high request rates, latency 100- 200 ms
  • Your application can achieve at least 3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD requests per second per prefix in a bucket.
  • There are no limits to the number of prefixes in a bucket.
  • If you spread reads across all four prefixes evenly, you can achieve  22,000 requests per second for GET and HEAD
  * S3 Byte-Range Fetches
    * • Parallelize GETs by requesting specific byte ranges
    * Better resilience in case of failures
    * Can be used to speed up downloads
    * Can be used to retrieve only partial data (for example the head of a file)
    
* KMS
  • If you use SSE -KMS, you may be impacted by the KMS limits
  • When you upload, it calls the GenerateDataKey KMS API
  • When you download, it calls the Decrypt KMS API
  • Count towards the KMS quota per second (5500, 10000, 30000 req/s based on region)

* Multi-Part upload
  • recommended for files >100MB, must use for files > 5GB
  • Can help parallelize uploads (speed up transfers)

* S3 Transfer Acceleration
  • Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in the target region
  • Compatible with multi-part upload

* Encyption
    • SSE-S3: encrypts S3 objects using keys handled & managed by AWS
         • SSE-S3: encryption using keys handled & managed by Amazon S3
         • Object is encrypted server side
         • AES-256 encryption type
          • Must set header: “x-amz-server-side-encryption": "AES256"
    • SSE-KMS: leverage AWS Key Management Service to manage encryption keys
          * SSE-KMS: encryption using keys handled & managed by KMS
          • KMS Advantages: user control + audit trail
         • Object is encrypted server side
          • Must set header: “x-amz-server-side-encryption": ”aws:kms"
    • SSE-C: when you want to manage your own encryption keys
       * SSE-C: server-side encryption using data keys fully managed by the customer outside of AWS
        • Amazon S3 does not store the encryption key you provide
       • HTTPS must be used
        • Encryption key must provided in HTTP headers, for every HTTP request made
    • Client Side Encryption
      • Client library such as the Amazon S3 Encryption Client
      • Clients must encrypt data themselves before sending to S3
      • Clients must decrypt data themselves when retrieving from S3
      • Customer fully manages the keys and encryption cycle

* Security
  * Block public access to buckets and objects
  * If you know your bucket should never be public, leave these on
  * Can be set at the account level
  * MFA Delete: MFA (multi factor authentication) can be required in versioned buckets to delete objects
  * Pre-Signed URLs: URLs that are valid only for a limited time (ex:premium video service for logged in users)

* S3 Select
  * Used for specific Object
  * Data Formats
    * CSV / JSON / PARQUET
  * Encoding
    * only utf8
  * Compression
    * GZIP, BZIP2 FOR CSV and JSON
    * GZIP columnar compression for parquet
  * Encryption
    * SSE-C 
    * SSE-S3
    * SSE-KMS
  * Works with Glacier
    * Only can used with Uncompressed CSV Files

* Event Notifications
  *S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectRestore, S3:Replication…
  • Object name filtering possible (*.jpg)
  • Use case: generate thumbnails of images uploaded to S3
  • S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer
  • If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent
  • If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket.

# Databases  (Not in Mind Map)

## Engines

* Relational
  * RDS 
  * Table Access
    * Row
      * OLTP - Online Transaction Processing
        * Used for rapid transactions
        * Protects data through transaction rollback
        * Ideal for low latency applications
    * Column
      * OLAP - Online Analytics Processing
        * Used for Analytics workloads
        * Manages large amounts of data
        * Handles complex long running query operations
* Key/Value
  * Memory Store (Elasticache -> Redis / Memcache)
    * Fast and Fragile
    * Memory access is must fast then disk access
    * All data can be lost if exists hardware failure
    * Redis has disk persistent as speed costs
  * DynamoDB
    * Flexible and Inflexible
    * Can be used in key Value or Document style
    * Data Model dictates style
    * Table structure cant e changed after creation
* Document
  * Greats for documents
  * Structure/semi-structured documents (JSON/YAML)
  * Metadata is stored with document records
  * DocumentDB-> Mongo
    * Can hit critical scale failures
  * DynamoDb
* Neptune -> Graph
  * Relational +
  * Data is structure as node connected to edges
  * Edges are shared attributes between nodes
  * can reveal otherwise hidden parameters in data
* Quantum Ledger Database (QLBB) -> Cannot delete record, can modify but the versions are tracked. Use for finantial
* Timestream - Time Series data store
* KeySpaces - Managed Apache Cassandra

## Dynamo DB

* Max Item size is 400 Kb
* WCU
  * One write capacity unit represents one write per second for an item up to 1 KB in size.
  • Example 1: we write 10 objects per seconds of 2 KB each.
     • We need 2 * 10 = 20 WCU
  • Example 2: we write 6 objects per second of 4.5 KB each
     • We need 6 * 5 = 30 WCU (4.5 gets rounded to the upper KB)
  • Example 3: we write 120 objects per minute of 2 KB each
  • We need 120 / 60 * 2 = 4 WCU

* RCU
  • One read capacity unit represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4 KB in size.
  • Example 1: 10 strongly consistent reads per seconds of 4 KB each
     • We need 10 * 4 KB / 4 KB = 10 RCU
  • Example 2: 16 eventually consistent reads per seconds of 12 KB each
     • We need (16 / 2) * ( 12 / 4 ) = 24 RCU
  • Example 3: 10 strongly consistent reads per seconds of 6 KB each
     • We need 10 * 8 KB / 4 = 20 RCU (we have to round up 6 KB to 8 KB)

* Throttling
  • If we exceed our RCU or WCU, we get ProvisionedThroughputExceededExceptions
  • Reasons:
     • Hot keys / partitions: one partition key is being read too many times (popular item for ex)
     • Very large items: remember RCU and WCU depends on size of items
  • Solutions:
     • Exponential back-off when exception is encountered (already in SDK)
     • Distribute partition keys as much as possible
     • If RCU issue, we can use DynamoDB Accelerator (DAX)

* Partitions Internal
  • You start with one partition
  • Each partition:
    • Max of 3000 RCU / 1000 WCU
    • Max of 10GB
  • To compute the number of partitions:
    • By capacity: (TOTAL RCU /3000) + (TOTAL WCU / 1000)
    • By size: Total Size / 10 GB
    • Total partitions = CEILING(MAX(Capacity, Size))
   • WCU and RCU are spread evenly between partitions

* Writing Data
  • PutItem - Write data to DynamoDB (create data or full replace)
    • Consumes WCU
  • UpdateItem – Update data in DynamoDB (partial update of attributes)
    • Possibility to use Atomic Counters and increase them
  • Conditional Writes:
    • Accept a write / update only if conditions are respected, otherwise reject
    • Helps with concurrent access to items
    • No performance impact

* Deleting Data
  • DeleteItem 
    • Delete an individual row 
    • Ability to perform a conditional delete 
  • DeleteTable 
     • Delete a whole table and all its items 
     • Much quicker deletion than calling DeleteItem on all items

* Batching Writes
   • BatchWriteItem
     • Up to 25 PutItem and / or DeleteItem in one call
     • Up to 16 MB of data written
     • Up to 400 KB of data per item
     • Batching allows you to save in latency by reducing the number of API calls done against DynamoDB
     • Operations are done in parallel for better efficiency
     • It’s possible for part of a batch to fail, in which case we have the try the failed items (using exponential back-off algorithm)

* Reading Data
  • GetItem:
    • Read based on Primary key
    • Primary Key = HASH or HASH-RANGE
    • Eventually consistent read by default
    • Option to use strongly consistent reads (more RCU - might take longer)
    • ProjectionExpression can be specified to include only certain attributes
  • BatchGetItem:
    • Up to 100 items
    • Up to 16 MB of data
    • Items are retrieved in parallel to minimize latency

* Query
   • Query returns items based on:
     • PartitionKey value (must be = operator)
     • SortKey value (=, <, <=, >, >=, Between, Begin) – optional
     • FilterExpression to further filter (client side filtering)
   • Returns:
     • Up to 1 MB of data
     • Or number of items specified in Limit
   • Able to do pagination on the results
   • Can query table, a local secondary index, or a global secondary index

* Scan
  • Scan the entire table and then filter out data (inefficient)
  • Returns up to 1 MB of data – use pagination to keep on reading
  • Consumes a lot of RCU
  • Limit impact using Limit or reduce the size of the result and pause
  • For faster performance, use parallel scans:
     • Multiple instances scan multiple partitions at the same time
     • Increases the throughput and RCU consumed
     • Limit the impact of parallel scans just like you would for Scans
  • Can use a ProjectionExpression + FilterExpression (no change to RCU)

* LSI
  * LSI must be defined at table creation time
  * Alternate range key for your table, local to the hash key
  * Up to five local secondary indexes per table
* GSI
  * The index is a new “table” and we can project attributes on it
  * Must define RCU / WCU for the index
  * Possibility to add / modify GSI (not LSI)
* DAX
  • DAX = DynamoDB Accelerator
  • Seamless cache for DynamoDB, no application re-write
  • Writes go through DAX to DynamoDB
  • Micro second latency for cached reads & queries
  • Solves the Hot Key problem (too many reads)
  • 5 minutes TTL for cache by default
  • Up to 10 nodes in the cluster
  • Multi AZ (3 nodes minimum recommended for production)
  • Secure (Encryption at rest with KMS, VPC, IAM, CloudTrail…)
* Streams
  • Changes in DynamoDB (Create, Update, Delete) can end up in a DynamoDB Stream
  • This stream can be read by AWS Lambda, and we can then do:
  • React to changes in real time (welcome email to new users)
  • Create derivative tables / views
  • Insert into ElasticSearch
  • Could implement Cross Region Replication using Streams
  • Stream has 24 hours of data retention
  • Configurable batch size (up to 1,000 rows, 6 MB)
  * Kinesis Adapter
    * • Use the KCL library to directly consume from DynamoDB Streams
      • You just need to add a “Kinesis Adapter” library
      • The interface and programming is exactly the same as Kinesis Streams
      • That’s the alternative to using AWS Lambda
* TTL
  • TTL = automatically delete an item after an expiry date / time
  • TTL is provided at no extra cost, deletions do not use WCU / RCU
  • TTL is a background task operated by the DynamoDB service itself
  • Helps reduce storage and manage the table size over time
  • Helps adhere to regulatory norms
  • TTL is enabled per row (you define a TTL column, and add a date there)
  • DynamoDB typically deletes expired items within 48 hours of expiration
  • Deleted items due to TTL are also deleted in GSI / LSI
  • DynamoDB Streams can help recover expired items
* Security & Other Features
  • Security:
     • VPC Endpoints available to access DynamoDB without internet
     • Access fully controlled by IAM
     • Encryption at rest using KMS
     • Encryption in transit using SSL / TLS
  • Backup and Restore feature available
     • Point in time restore like RDS
     • No performance impact
  • Global Tables
     • Multi region, fully replicated, high performance
  • Amazon Database Migration Service (DMS) can be used to migrate to DynamoDB (from Mongo, Oracle, MySQL, S3, etc…)
  • You can launch a local DynamoDB on your computer for development purposes
* 

# Elasticache  (Not in Mind Map)

• The same way RDS is to get managed Relational Databases…
• ElastiCache is to get managed Redis or Memcached
• Caches are in-memory databases with really high performance, low latency
• Helps reduce load off of databases for read intensive workloads
• Helps make your application stateless
• Write Scaling using sharding
• Read Scaling using Read Replicas
• Multi AZ with Failover Capability
• AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups

## Redis

• Redis is an in-memory key-value store
• Super low latency (sub ms)
• Cache survive reboots by default (it’s called persistence)
• Great to host
   • User sessions
   • Leaderboard (for gaming)
   • Distributed states
   • Relieve pressure on databases (such as RDS)
   • Pub / Sub capability for messaging
• Multi AZ with Automatic Failover for disaster recovery if you don’t want to lose your cache data
• Support for Read Replicas

## Memcached

• Memcached is an in-memory object store
• Cache doesn’t survive reboots
• Use cases:
   • Quick retrieval of objects from memory
   • Cache often accessed objects
   • Overall, Redis has largely grown in popularity and has better feature sets than Memcached.

# Kinesis

* Managed Alternative to Apache Kafka
* Great for application logs, metrics, IoT, clickstreams
* Great for real time big data
* Great for streaming processing frameworks (Spark, NiFi, etc...)
* Data is automatically replicated synchronously to 3 Az

## Kinesis Data Streams

* Low latency streaming ingest at scale
* Data Records are stored for 24 hours to 365 Days
  * Default 24 Hours
  * Data Retention to 70 Days enabling extended data retention
  * Data Retention to 365 Days enabling long term data retention
* Ability to reprocess / replay data
  * Multiple applications can consume the same stream
  * Real time processing with scale of throughput
* Once data is inserted in Kinesis, it can't be delete (immutability)
* Shard
  * Records are ordered per shard
  * Each shard consists of a sequence of data records. These can be ingested at 1000 records per second
  * Record
    * Data Blob: Data payload per record can be up to 1 mega
    * Record Key
      * Sent alongside a record, helps to group records in shards. Same Key = Same shard
      * Use highly distributed key to avoid the hot partition problem
    * Sequence number
      * unique identifier for each records put in shards. Added by kinesis after ingestion
  * 1 Shard
    * Producer
      * 1 MB/Sec Input or 1000 messages at write
      * ProvisionedThroughtputException otherwise
    * Consumer Classic
      * 2 MB/Sec Output across all consumers
      * 5 API calls per second across all consumers
    * Consumer Enhanced Fan-Out
      * 2 MB/Sec Output per enhanced consumer
      * No API calls needed (push model)
* Producers
  * Kinesis Producer Library (KPL)
    * Layer of abstraction specially for Ingesting data
    * Automatic And configurable retry
    * Use cases: high performance, long-running producers
    * Synchronous or Asynchronous API (better performance for async)
    * Submits metrics to CW for monitoring
    * Batching (both turned on by default) - increase throughput, decrease cost
      * We can influence the batching efficiency by introducing some delay with RecordMaxBufferedTime (default 100ms)
      * Collect
        * Collect Records and write to multiple shards in the same PutRecords API Call
      * Aggregate - increased latency
        * Capability to store multiple records in one record
        * Increase payload size and improve throughput
    * Compression must be implemented by the user
    * Java Wrapper
    * KPL records must be de-coded with KCL or special helpers library
    * Don't use in applications that cannot tolerate additional delays. This applications may need to use the AWS SDK directly
  * Kinesis API (SDK) 
    * API Calls (Send records using PutRecord or PutRecords)
    * PutRecords
      * Uses batching and increases throughtput => less HTTP requests
    * ProvisionedThroughtputExceed if we go over the limits
      * Happens when sending more data (exceeding MB/s or Records/s for any shard)
      * Make sure you don't have a hot shard (partition key is bad)
      * Solution
        * Retries with backoff
        * Increase shards (scaling)
        * Ensure your partition key is a good one
    * Stream Creation, resharding, and putting and getting records are manually handled
    * No delays on processing
    * Any AWs SDK
    * Use case: low throughput, higher latency, simple API, AWS Lambda
    * Managed AWS Sources for Kinesis Data Streams
      * Cloudwath Logs
      * AWS IoT
      * Kinesis Data Analytics
  * Kinesis Agent 
    * Pre Built Java to be installed on Linux
    * Monitor Log files and sends them to Kinesis Data Streams
    * Only linux
    * Features
      * Write from multiple directories and multiple streams
      * Routing features based on directory /lof file
      * Pre process data before sending to streams (single line, csv to json, log to json)
      * Handles file rotation, checkpointing and retry upon failures
      * Emits metrics to CW
  * 3rd party libraries: Spark, Log4j Appenders, Flunne, Kafka Connect NiFi
* Consumers
  * Kinesis SDK
    * Records are polled by consumers from a shard (GetRecords)
    * Get Records returns up 10Mb of data (then throttle for 5 seconds) or up 10000 records
    * Maximum of 5 GetRecords APi calls per shard per second = 200 ms latency
    * If 5 consumers application consume from the same shard, means every consumer can poll once a second and receive less than 400Kbs
  * Kinesis Client Lib (KCL)
    * Java first library but exists to other languagues (Golang, python, Ruby, ....)
    * Read Records from Kinesis produced with the KPL (de-aggregation)
    * Share multiple shards with multiple consumers in one group, shard discovery
    * Checkpointing feature to resume progress
    * Leverages DynamoDB for coordination and checkpoiting (one row per shard)
      * Make sure your provision enough WCU/RCU
      * Or use on-demand for DynamoDB
      * Otherwise DynamoDB may slow down KCL
    * Record processores will process the data
    * ExpiredIteratorException => increase WCU
  * Kinesis connector Library
    * Deprecated => Older java library leverages the KCL library
    * Write data to
      * S3
      * DynamoDB
      * Redshift 
      * Elasticsearch
    * Kinesis Firehose replaces the connector library for a few of these targets, lambda for the others
  * 3 rd Party libraries
    * Spark, Log4j Appenders, Flume, Kafka Connect
  * Kinesis Firehose
  * AWS Lambda
    * Lambda consumer has a library to de-aggregate record from the KPL
    * Run be used to run Lightweight ETL to
      * S3
      * DynamoDB
      * Redshift
      * ElasticSearch
      * Anywhere you want
    * Can be used to trigger notifications / send emails in real time
    * Lambda has a configurable batch size
  * Kinesis consumer Enhanced Fan-Out
    * Works with KCL 2.0 and AWs Lambda (Nov 2018)
    * The consumer subscribes to shard
    * Each Consumer get 2MB/s of provisioned throughtput per shard
      * That means 20 Consumers will get 40Mb/s per shard aggregated
      * No more 2 MB/s Limit
    * Enhanced Fan out: Kinesis pushes data to consumers over HTTP/2
    * Reduce Latency (~70ms)
  * Standard consumers vs Enhanced Fan Out
    * Standard
      * Low number of consuming applications (1,2,3, ..)
      * Can tolerate 200 ms latency
      * Minimize cost
    * Enhanced Fan Out
      * Multiple consumer applications for the same stream
      * Low Latency requirements (70ms)
      * Higher costs
      * Default Limit of 5 consumers using enhanced fan out per data stream
* Scaling
  * Add Shard (Shard Splitting)
    * Can be used to increase the Stream Capacity (1Mb/s data in per shard)
    * Can be used to divide a hot shard
    * The old shard is closed and will be deleted once the data is expired
    * Out-Order records after resharding
      * After reshard, you can read from child shards
      * However, data you haven't read yet could still be in the parent
      * If you start reading the child before completing reading the parent, you cold read data for a particular hash key out of order
      * After reshard, read entirely from the parent until you don't have new records
      * Note: The KCL has this logic already built-in, event after resharding operations
      * This can also happen in merging shards
  * Merging Shards
    * Decrease the Stream capacity and save costs
    * Can be used to group two shards with low traffic
    * Old Shards are closed and deleted based on data expiration
  * Auto-Scaling
    * Auto Scaling is not a native feature
    * The api call to change the number os shards is UpdateShardcount
    * We can implement Auto Scaling with AWs Lambda (trigger lambda based CW Alarm)
  * Scaling Limitations
    * Resharding cannot be done in Parallell. Plan capacity in advance
    * You can only perform one resharding operation at time and takes a few seconds
    * For 1000 shards, it takes 30K seconds (8.3 hours) to double the shards to 2000
* Handling Duplicates
  * For Producers
    * Producer retries can create duplicates due to network timeouts
    * Altough the two records have identical data, they also have unique sequence numbers
    * Fix: embed unique record ID in the data to de-duplicate on the consumer side
  * For Consumers
    * Consumer Retries can make your application read the same data twice
    * Consumer Retries happen when record processores restart
      * A worker terminates unexpectedly
      * Worker instances are added of removed
      * Shards are merged or split
      * The application is deployed
    * Fixes
      * Make your consumer application idempotent
      * If the final destination can handle duplicates, it's recommended to do it there
* Kinesis Security
  * Control Access / authorization using IAM Policies
  * Encryption in flight using HTTPS endpoints
  * Encryption at rest using KMS
  * Client side encryption must be manually implemented (harder)
  * VPC Endpoints available for Kinesis to access within VPC
* Lambda + Kinesis
  • Your Lambda code receives an event with a batch of stream records
    • You specify a batch size when setting up the trigger(up to 10,000 records)
    • Too large a batch size can cause timeouts! 
    • Batches may also be split beyond Lambda’s payload limit (6 MB)
  • Lambda will retry the batch until it succeeds or the data expires 
     * This can stall the shard if you don’t handle errors properly
     • Use more shards to ensure processing isn’t totally held up by errors
  • Lambda processes shard data synchronously

## Kinesis Firehose

* Record can be large as 1000KB
* Accumulates records in buffer
  * Buffer Interval
    * 60 to 900 Seconds
    * Near Real Time (60 seconds latency minimum for non full batches)
    * Possible Time delay due Buffer Size and Buffer Interval
    * Batch Writes (Near Real-Time)
  * Buffer Size
    * Example 32Mb: If that buffer size is reached, it's flushed
    * Firehose can automatically increase the buffer size to increase throughput
  * Resume
    * High Throughput => Buffer size will be hit
    * Low throughput =< Buffer time will be hit
* Sources
  * Applications
  * Client
  * SDK, KPL
  * Kinesis Agent
  * Kinesis Data Streams
  * Amazon Cloudwatch (Logs & Events)
  * AWS IOT
* Can transform Data with Lambda
  * Example: CSV => JSON
* Transformation Failures or Delivery Failures can be archived in other S3 Bucket
* Destinations
  * S3
    * Destination Bucket with transformed records with lambda
    * Can convert from JSON to Parquet / ORC
    * Backup bucket to store original records
    * Can Apply compression
      * GZIP, ZIP and Snappy
    * Buffer Size
      * 1 MB <-> 128 Mb
    * Data Organized -> Uses the UTC time prefix in the format : YYYY/MM/DD/HH
      * File: DeliveryStreamName-DeliveryStreamVersion-YYYY-MM-DD-HH-MM-SS-RandomString
  * RedShift
    * First goes to S3 buckets and is copy to redshift
    * Can run lambdas to transform records before storing in s3 or before storing in redshift
    * Support backup bucket to original records
    * Supports GZIP Compression
  * Elasticsearch
    * Backup bucket
    * Transform record with lambda
    * Buffer Size
      * 1 MB <-> 100 Mb
  * Splunk Instance, New Relic, DataDog, MongoDB
      * Backup bucket
      * Transform record with lambda
  * HTTP Endpoint
* Vs Data Streams
  * Streams
    * Going to write custom code (producer / consumer)
    * Real time (~200 ms latency for classic, ~70 ms latency for enhanced fan-out)
    * Must manage scaling (shard splitting / merging)
    * Data Storage for 1 to 365 days, replay capability, multi consumers
    * Use with Lambda to insert data in real-time to ElasticSearch (for example)
  * Firehose
    * Fully managed, send to S3, Splunk, Redshift, ElasticSearch
    * Serverless data transformations with Lambda
    * Near real time (lowest buffer time is 1 minute)
    * Automated Scaling
    * No data storage

## Kinesis Video Streams

* Stored in S3
* Support AWS Rekognition Video
* Support SNS
* Producers:
  * Uses Kinesis Video Stream Libraries
    * Installed on device to secure connection to kinesis video stream
    * Manages media sources and the stream lifecycle data flows from a media source kinesis video streams
    * Can Store Data or Analyze real time
* Consumers
  * EC2
  * AWS Rekognition
  * AWS Services
  * Third Party Services

## Kinesis Data Analytics

* perform real-time analytics on streams using SQL
* Run SQL Queries from Inputs and Produce output
  * Supports SQL and Apache Flink Runtimes
    * supports Scala as well as Java
    * Flink is a framework for processing data streams
    * Instead of using SQL, you can develop your own Flink application from scratch and load it into KDA via S3
  * Supports Reference Data
    * In S3
  * Output Format CSV / JSON
* Input Streams
  * Kinesis Data Streams
  * Kinesis Data Firehose
  * S3
* Support Error Handling
* Pre Processing with AWS Lambda
* Output Streams
  * Kinesis Data Streams
    * Lambda
  * Kinesis Data Firehose
    * S3
    * Redshift
    * ...
  * Lambda
    * Allows lots of flexibility for post-processing 
        • Aggregating rows 
        • Translating to different formats 
        • Transforming and enriching data 
        • Encryption
    * Opens up access to other services & destinations
      * S3, DynamoDB, Aurora, Redshift, SNS, SQS, CloudWatch
* Use cases
  * Responsive real-time analytics
    * Example: Uncover real-time monitoring metrics such as response time and error-rate spikes
  * Managing real-time gaming metrics
    * Example: Aggregate and join important gaming data like gamers account and min, max and average time playing the game over a 10 minutes sliding window
* RANDOM_CUT_FOREST
  SQL function used for anomaly detection on numeric columns in a stream
  • It’s a novel way to identify outliers in a data set so you can handle them however you need to
  • Example: detect anomalous subway ridership during the NYC marathon

# SQS

• Oldest offering (over 10 years old)
• Fully managed
• Scales from 1 message per second to 10,000s per second
• Default retention of messages: 4 days, maximum of 14 days
• No limit to how many messages can be in the queue
• Low latency (<10 ms on publish and receive)
• Horizontal scaling in terms of number of consumers
• Can have duplicate messages (at least once delivery, occasionally)
• Can have out of order messages (best effort ordering)
• Limitation of 256KB per message sent
* Producers
  * Define Body 
  * Add message attributes (metadata – optional)
  • Provide Delay Delivery (optional)
  • Get back 
      * Message identifier 
      * MD5 hash of the body
* Consumers
  • Poll SQS for messages (receive up to 10 messages at a time)
  • Process the message within the visibility timeout
  • Delete the message using the message ID & receipt handle

* Fifo Queue
  * Name of the queue must end in .fifo 
  * Lower throughput (up to 3,000 per second with batching, 300/s without)
  * Messages are processed in order by the consumer
  * Messages are sent exactly once 
    * 5-minute interval de-duplication using “Duplication ID”

* SQS Extended Client
  * To send large messages
  * Use SQS Extended client (Java Library)
  * The large message is sent to S3, and one small metadata message to Queue
  * The consumer reads from queue the small message and retrieve the large message from S3

* Use Cases
  • Decouple applications(for example to handle payments asynchronously)
  • Buffer writes to a database (for example a voting application)
  • Handle large loads of messages coming in (for example an email sender)
  • SQS can be integrated with Auto Scaling through CloudWatch!

* Limits
  • Maximum of 120,000 in-flight messages being processed by consumers
  • Batch Request has a maximum of 10 messages – max 256KB
  • Message content is XML, JSON, Unformatted text
  • Standard queues have an unlimited TPS
  • FIFO queues support up to 3,000 messages per second (using batching)
  • Max message size is 256KB (or use Extended Client)
  • Data retention from 1 minute to 14 days
  • Pricing:
    • Pay per API Request
    • Pay per network usage

* Security
  • Encryption in flight using the HTTPS endpoint
  • Can enable SSE (Server Side Encryption) using KMS
  • Can set the CMK (Customer Master Key) we want to use
  • SSE only encrypts the body, not the metadata (message ID, timestamp, attributes)
  • IAM policy must allow usage of SQS
  • SQS queue access policy
  • Finer grained control over IP
  • Control over the time the requests come in

* Vs Kinesis Data Stream
  • Kinesis Data Stream:
    • Data can be consumed many times
    • Data is deleted after the retention period
    • Ordering of records is preserved (at the shard level) – even during replays
    • Build multiple applications reading from the same stream independently (Pub/Sub)
    • “Streaming MapReduce” querying capability
    • Checkpointing needed to track progress of consumption
    • Shards (capacity) must be provided ahead of time
    * Kinesis Data Streams Use cases : • Fast log and event data collection and processing • Real Time metrics and reports • Mobile data capture • Real Time data analytics • Gaming data feed • Complex Stream Processing • Data Feed from “Internet of Things”
  * SQS
    • Queue, decouple applications
    • One application per queue
    • Records are deleted after consumption (ack / fail)
    • Messages are processed independently for standard queue
    • Ordering for FIFO queues
    • Capability to “delay” messages
    • Dynamic scaling of load (noops)
    • SQS Use cases : 
       • Order processing • Image Processing • Auto scaling queues according to messages. • Buffer and Batch messages for future processing. • Request Offloading

# IoT  (Not in Mind Map)

## IoT Device Gateway

• Serves as the entry point for IoT devices connecting to AWS
• Allows devices to securely and efficiently communicate with AWS IoT
• Supports the MQTT, WebSockets, and HTTP 1.1 protocols
• Fully managed and scales automatically to support over a billion devices
• No need to manage any infrastructure

## IoT Message Broker

• Pub/sub (publishers/subscribers) messaging pattern - low latency
• Devices can communicate with one another this way
• Messages sent using the MQTT, WebSockets, or HTTP 1.1 protocols
• Messages are published into topics (just like SNS)
• Message Broker forwards messages to all clients connected to the topic

## Thing Registry

• All connected IoT devices are represented in the AWS IoT registry
• Organizes the resources associated with each device in the AWS Cloud
• Each device gets a unique ID
• Supports metadata for each device (ex: Celsius vs Fahrenheit, etc…)
• Can create X.509 certificate to help IoT devices connect to AWS
• IoT Groups: group devices together and apply permissions to the group

## Authentication

3 possible authentication methods for Things: 
  • Create X.509 certificates and load them securely onto the Things
  • AWS SigV4 
  • Custom tokens with Custom authorizers
• For mobile apps: 
  • Cognito identities (extension to Google, Facebook login, etc…)
• Web / Desktop / CLI: 
  • IAM 
  • Federated Identities

## Authorization

• AWS IoT policies: 
  • Attached to X.509 certificates or Cognito Identities 
  • Able to revoke any device at any time 
  • IoT Policies are JSON documents 
  • Can be attached to groups instead of individual Things. 

• IAM Policies
  • Attached to users, group or roles 
  • Used for controlling IoT AWS APIs

## Device Shadow

• JSON document representing the state of a connected Thing
• We can set the state to a different desired state (ex: light on)
• The IoT thing will retrieve the state when online and adapt

## Rules Engine

* Rules are defined on the MQTT topics
• Rules = when it’s triggered | Action = what is does
• Rules use cases:
• Augment or filter data received from a device
• Write data received from a device to a DynamoDB database
• Save a file to S3
• Send a push notification to all users using SNS
• Publish data to a SQS queue
• Invoke a Lambda function to extract data
• Process messages from a large number of devices using Amazon Kinesis
• Send data to the Amazon Elasticsearch Service
• Capture a CloudWatch metric and Change a CloudWatch alarm
• Send the data from an MQTT message to Amazon Machine Learning to make predictions based on an Amazon ML model & more
• Rules need IAM Roles to perform their actions

## IoT Greengrass

• IoT Greengrass brings the compute layer to the device directly
• You can execute AWS Lambda functions on the devices: 
  • Pre-process the data 
  • Execute predictions based on ML models 
  • Keep device data in sync 
  • Communicate between local devices
• Operate offline 
• Deploy functions from the cloud directly to the devices

# Cloudwatch Logs Subscriptions Filters  (Not in Mind Map)

* Can stream CloudWatch Logs into
  * Kinesis Data Streams
    * Logs Real Time Analytics
      * Create subscription filter to Data Streams and Send to Data Analytics and then lambda if required
  * Kinesis Data Firehose
    * Send Logs to ES Near Real Time
      * Create subscription filter to Firehose and send to ES
      * Can use lambda to transform data
  * AWS Lambda
    * Send Logs to ES Real Time
      * Create subscription filter to Lambda and send to ES
* Using CloudWatch Logs Subscriptions Filters
* You can enable them using the AWS CLI

# MSK (Kafka)

* Publish and Subscribe to stream of records, similiar to a message queue
* Stores Streams of records in a durable way
* Process streams of records as they occur
• Fully managed Apache Kafka on AWS
• Allow you to create, update, delete clusters
• MSK creates & manages Kafka brokers nodes & Zookeeper nodes for you
• Deploy the MSK cluster in your VPC, multi-AZ (up to 3 for HA)
• Automatic recovery from common Apache Kafka failures
• Data is stored on EBS volumes
• You can build producers and consumers of data
• Can create custom configurations for your clusters
• Default message size of 1MB
• Possibilities of sending large messages (ex: 10MB) into Kafka after custom configuration

* configurations
  • Choose the number of AZ (3 – recommended, or 2)
  • Choose the VPC & Subnets
  • The broker instance type (ex: kafka.m5.large)
  • The number of brokers per AZ (can add brokers later)
  • Size of your EBS volumes (1GB – 16TB)

* Security
  • Encryption:
    • Optional in-flight using TLS between the brokers
    • Optional in-flight with TLS between the clients and brokers
    • At rest for your EBS volumes using KMS
  • Network Security:
    • Authorize specific security groups for your Apache Kafka clients
  • Authentication & Authorization - Define who can read/write to which topics
    • Mutual TLS (AuthN) + Kafka ACLs (AuthZ)
    • SASL/SCRAM (AuthN) + Kafka ACLs(AuthZ)
    • IAM Access Control (AuthN + AuthZ)

* Monitoring
  • CloudWatch Metrics
    • Basic monitoring (cluster and broker metrics)
    • Enhanced monitoring (++enhanced broker metrics)
    • Topic-level monitoring (++enhanced topic-level metrics)
  • Prometheus (Open-Source Monitoring)
    • Opens a port on the broker to export cluster, broker and topic-level metrics
    • Setup the JMX Exporter (metrics) or Node Exporter (CPU and disk metrics)
  • Broker Log Delivery
    • Delivery to CloudWatch Logs
    • Delivery to Amazon S3
    • Delivery to Kinesis Data Streams

* Versus Kinesis Data Streams
  

* Vs Kinesis Data Streams
  * Kinesis Data Streams
    • 1 MB message size limit
    • Data Streams with Shards
    • Shard Splitting & Merging
    • TLS In-flight encryption
    • KMS At-rest encryption
    * * Kinesis Retention Time is 1 day with 7 Max and have deep AWS Integration
    * MSK retention time is 7 Days (Max unlimited). Strong third party tooling
    * Scaling for Kinesis Data Streams is based in throughput provisioning model (increase shards)
    * Scaling for MSK is based cluster provisioning model (add instances)
    * MSK has strong 3rd party tooling and Kinesis Data streams deep aws integration
    • Security:
        • IAM policies for AuthN/AuthZ
  * MSK
    • 1MB default, configure for higher (ex: 10MB)
    • Kafka Topics with Partitions
    • Can only add partitions to a topic
    • PLAINTEXT or TLS In-flight Encryption
    • KMS At-rest encryption
    • Security:
      • Mutual TLS (AuthN) + Kafka ACLs (AuthZ)
      • SASL/SCRAM (AuthN) + Kafka ACLs (AuthZ)
      • IAM Access Control (AuthN + AuthZ)

# Migrate Data to AWS  (Not in Mind Map)

* Network Connectivity < 10 Mbps and Amount Data < 500 Gigas => Method = unmanaged (cli/sdk)
* Network Connectivity > 10 Mbps and Amount Data > 500 Gigas => Method = Managed
* Hybrid Cloud
  * Direct Connect ( 1 ou 10 Gbps)
* Online Data Transfer
  * AWS DataSync
    * Automate moving data between on-premises storage and s3, EFS, FSX for windows files server
    * Don't need S3 transfer acceleration
  * FTP/SFTP/FTPS
    * Fully manage support for file transfer directly into and out of S3
  * S3 transfer acceleration
    * A faster way to use public internet to transfer data into s3
  * Kinesis data firehose
  * Snowcone
    * Collect, proccess and move data to AWS online with AWSDataSync
* Offline
  * Snowcone
    * can be online or ship device to offline transfer
  * Snowball
    * 80 TB (10Gb Network) -> Network encrypted end-to-end
    * Used for petabyte-scale data transport with import and export to S3
    * User interface similiar to S3
  * Snowball Edge
    * Network encrypted end-to-end
    * Local Storage and large-scale data transfer
    * Local Lambda and EC2 Instance compute and AWS IoT Greengrass
  * Snowmobile
    * Network encrypted end-to-end
    * An exabyte-scale data transport solution that uses a secure semi 40-foot shipping containr to transfer large amounts of data into and out AWS

## Database migration Service

* Can be used to upgrade database
* Archive Old data
* Migrate from NoSQL to SQL , SQL to NoSQl or SQL to SQL (heterogeneous and homogenous migrations)
* Can be usd to Migrate to S3
* Managed replication
  * Cross Region replication
  * Offload Analytics
  * Keeping data In sync
* Upgrade
  * With DMS you can upgrade versions of your database software easily with no downtime
* Continuous Data Replication using CDC
  * Don't Support Views
* It's necessary create EC2 Instance to perform the replication tasks
* AWS Schema Conversion Tool (SCT)
  * Convert your Database’s Schema from one engine to another
  * Prefer compute-intensive instances to optimize data conversions
  * You do not need to use SCT if you are migrating the same DB engine

## Data Pipeline

* Helps you process and move data between AWS compute and storage services as well as on-premises data sources
* Can create ETL Workflow to automate the processing and movement of data at scheduled intervals
* Provisioned and terminated automatically
* Retries and notifies on failures
* Cross-region pipelines
* Destinations include S3, RDS, DynamoDB, Redshift and EMR
  * Data sources may be on-premises
* Key concepts
  * Data Notes ( where data is)
  * Activities (Action)
    * EMR
    * Hive
    * Copy
    * SQL
    * Custom Script/ on prem
  * Pre conditions (that must be met before the activity is ran)
    * DynamoDBDataExists
    * DynamoDBTableExists
    * S3KeyExists
    * S3PrefixExists
    * ShellCommandPrecondition
    * Custom Pre Conditions
  * Schedules
    * Install task runner
* On - Premises
  * Install Task runner (Polls Tasks from Data Pipeline)
* Use Cases
  * On-Premises Database for Analytics and BI Tools
    * Move tables between a production database running on RDS and a non-production MySql database running on-premises for Bi purposes
  * Remotely Executing Stored Procedures
    * Utilize stored procedures and run them on your scheduled tasks for your on-premises databases

## Snow Family

Today, you can use AWS OpsHub (a software you install on your computer / laptop) to manage your Snow Family Device
• Unlocking and configuring single or clustered devices
• Transferring files
• Launching and managing instances running on Snow Family Devices
• Monitor device metrics (storage capacity, active instances on your device)
• Launch compatible AWS services on your devices (ex: Amazon EC2 instances, AWS DataSync, Network File System (NFS))

### Snowball Edge 

Physical data transport solution: move TBs or PBs of data in or out of AWS
• Alternative to moving data over the network (and paying network fees)
• Pay per data transfer job • Provide block storage and Amazon S3 -compatible object storage
• Snowball Edge Storage Optimized • 80 TB of HDD capacity for block volume and S3 compatible object storage
• Snowball Edge Compute Optimized • 42 TB of HDD capacity for block volume and S3 compatible object storage
• Use cases: large data cloud migrations, DC decommission, disaster recovery

### SnowCone

• Small, portable computing, anywhere, rugged & secure, withstands harsh environments
• Light (4.5 pounds, 2.1 kg) • Device used for edge computing, storage, and data transfer
• 8 TBs of usable storage • Use Snowcone where Snowball does not fit(space -constrained environment)
• Must provide your own battery / cables 
• Can be sent back to AWS offline, or connect it to internet and use AWS DataSync to send data

### Snowmobile

• Transfer exabytes of data (1 EB = 1,000 PB = 1,000,000 TBs)
• Each Snowmobile has 100 PB of capacity (use multiple in parallel)
• High security: temperature controlled, GPS, 24/7 video surveillance
• Better than Snowball if you transfer more than 10 PB

## Direct Connect

• Provides a dedicated private connection from a remote network to your VPC
• Dedicated connection must be setup between your DC and AWS Direct Connect locations
• You need to setup a Virtual Private Gateway on your VPC
• Access public resources (S3) and private (EC2) on same connection
• Use Cases:
  • Increase bandwidth throughput - working with large data sets – lower cost
  • More consistent network experience - applications using real-time data feeds
   • Hybrid Environments (on prem + cloud)
• Supports both IPv4 and IPv6
* Connection Types
  • Dedicated Connections: 1Gbps and 10 Gbps capacity
     • Physical ethernet port dedicated to a customer
     • Request made to AWS first, then completed by AWS Direct Connect Partners
  • Hosted Connections: 50Mbps, 500 Mbps, to 10 Gbps
     • Connection requests are made via AWS Direct Connect Partners
     • Capacity can be added or removed on demand
     • 1, 2, 5, 10 Gbps available at select AWS Direct Connect Partners
  • Lead times are often longer than 1 month to establish a new connection
* Encryption
  • Data in transit is not encrypted but is private
  • AWS Direct Connect + VPN provides an IPsec-encrypted private connection
  • Good for an extra level of security, but slightly more complex to put in place
* Resiliency
  * One connection at multiple locations
  * Maximum resilience is achieved by separate connections terminating on separate devices in more than one location.
* Direct connect Gateway
  * if you want to setup a Direct Connect to one or more VPC in  many different regions (same account), you must use a  Direct Connect Gateway

## Lambda

* 10 x concurrent executions
  * synchronous - all sources
  * asynchronous - non aws sources
* Unlimited
  * asynchronous - AWs Sources
* Payload 
  * synchronous = 6 MB 
  * asynchronous = 256 MB
* Integration with Kinesis
  * One shard = Invoke One Lambda Function
* Scaling
  * Split child shards into parent shards

# EMR

## Primary Nodes

* Single Node or Multi Primary Nodes
  * Three primary nodes are only supported with EMR Version 5.23.0+
* Manages the cluster resources
  * The primary node coordinates the distribution and parallel execution of MapReduce executables
* Tracks and Directs HDFS
  * Knows hot to lookup files and track data that run on the core nodes
* Yarn resource Management 
  * EMR Uses yarn to centrally manage cluster resources of multiple data-processing frameworks
* Monitors Core and Task Nodes Health
  * Tracks the status of jobs submitted to the cluster and monitors the health of the core and task nodes

## Core Nodes

* Run tasks for the primary node
  * Such as Hadoop Map Reduce Tasks, Hive scripts and spark executors
* Coordinates data storage
  * Knows where and how store data. Data is stored on HDFS or EMRFS (Don't have caching)
  * Data node daemon runs here
* Multiple core Nodes only one core instance group
* Can be scaled up & down, but with some risk data loss

## Task Node

* Optional helpers
  * To add power to perform parallel computations
* No HDFS on data Nodes daemon
* Added and removed from running clusters

## Storage Options

* Local file system (Instance Store)
  * Ephemeral
  * Use for very high I/O performance / low cost
  * Temporary data (cache, buffers, scratch data)
  * Size/speed is determined by instance type
* EBS (Local File System)
  * Used to extend HDFS but is ephemeral
  * Used to add more storage to HDFS
  * EBS volumes can only be attached when launching a cluster
  • If you manually detach an EBS volume, EMR treats that as a failure and replaces it
* HDFS
  * Hadoop Distributed File System
  * Access : hdfs://
  * Fast but ephemeral distributed storage for EMR
  * Best for caching the intermediate results from flow steps
  * Great jobs with iterative reads on the same dataset or Disk I/O intensive workloads
  * Multiple copies stored across cluster instances for redundancy
  • Files stored as blocks (128MB default size)
  * Hadoop tries to process data where it is stored on HDFS
* EMRFS
  * Works well for jobs that read a dataset once per run 
  * Persistent Storage
  * access S3 as if it were HDFS
  * Read and Write files directly to S3
  * How to access. S3://
  * Best used for persistent store and S3 features that are needed, like server side encryption and consistency
  * Natively supported by big data frameworks (Spark, Hive, Presto, etc)
  * Decouple storage and compute
    * No need to run compute clusters for storage (Unlike HDFS)
    * Can Run Transient EMR Clusters with Spot Instances
    * Multiple & Heterogeneous analysis clusters and services can use the same data
  * Designed for 99.999999999% durability
  * No need to pay for data replication

## Single AZ Concept

* Block Replication faster
* Communication between nodes faster
* Access to metadata
* Launch replacement cluster easily

## Spark

* Integrates with Kinesis
* Integrates with Redshift

## Hive

* Data Catalog for several sources
* Uses familiar SQL syntax (HiveQL)
• Interactive 
* • Scalable – works with “big data” on a cluster 
  * • Really most appropriate for data warehouse applications
• Easy OLAP queries – WAY easier than writing MapReduce in Java
• Highly optimized 
• Highly extensible 
  * • User defined functions 
  * • Thrift server 
  * • JDBC / ODBC driver

• Load table partitions from S3
• “alter table recover partitions”
• Write tables directly to S3
• Load scripts from S3
• DynamoDB as an external table
  • Read/write access
  • Copy to/from HDFS
  • Perform JOIN’s on DynamoDB

## Pig

* Higher level to Map Reduce
* Writing mappers and reducers by hand takes a long time.
• Pig introduces Pig Latin, a scripting language that lets you use SQL like syntax to define your map and reduce steps.
• Highly extensible with user defined functions (UDF’s)
* Query data in S3
* Load Jar scripts from s3
* Directly writing HCatalog tables in S3
* Submitting work from EMR Console using PIG scripts stored in S3

## Hbase

• Non-relational, petabyte -scale database
• Based on Google’s BigTable, on top of HDFS 
• In-memory 
• Hive integration
* Can store data (StoreFiles and metadata) on S3 Via EMRFs
* Can backup to S3
* HBase read-replicas on s3

## Presto

• It can connect to many different “big data” databases and data stores at once, and query across them
• Interactive queries at petabyte scale
• Familiar SQL syntax
• Optimized for OLAP – analytical queries, data warehousing
• Exposes JDBC, Command-Line, and Tableau interfaces
* Connectors
  • HDFS • S3 • Cassandra • MongoDB • HBase • SQL • Redshift • Teradata

## Zeppelin

• If you’re familiar with iPython notebooks – it’s like that
  • Lets you interactively run scripts / code against your data
  • Can interleave with nicely formatted notes
  • Can share notebooks with others on your cluster
• Spark, Python, JDBC, HBase, Elasticsearch + more
• Can run Spark code interactively (like you can in the Spark shell) • This speeds up your development cycle • And allows easy experimentation and exploration of your big data
• Can execute SQL queries directly against SparkSQL
• Query results may be visualized in charts and graphs
• Makes Spark feel more like a data science tool!

## EMR Notebook

• Similar concept to Zeppelin, with more AWS integration
• Notebooks backed up to S3
• Provision clusters from the notebook!
• Hosted inside a VPC 
• Accessed only via AWS console

## Hue

• Hadoop User Experience
• Graphical front-end for applications on your EMR cluster
• IAM integration: Hue Super - users inherit IAM roles
• S3: Can browse & move data between HDFS and S3

## Splunk

Splunk / Hunk “makes machine data accessible, usable, and valuable to everyone”
• Operational tool – can be used to visualize EMR and S3 data using your EMR Hadoop cluster.
• Reserved instances on 64-bit OS recommended
• A public AMI of Splunk Enterprise is available

## Flume

• Another way to stream data into your cluster 
• Made from the start with Hadoop in mind 
• Built-in sinks for HDFS and HBase
• Originally made to handle log aggregation

## MXNet

• Like Tensorflow, a library for building and accelerating neural networks
• Included on EMR

## S3DistCP

• Tool for copying large amounts of data 
   • From S3 into HDFS 
   • From HDFS into S3
• Uses MapReduce to copy in a distributed manner
• Suitable for parallel copying of large numbers of objects 
   • Across buckets, across accounts
* Dont support concat Parquet Files

## Hadoop Tools

• Ganglia (monitoring) => Operational Dashboard
• Mahout (machine learning)
• Accumulo (another NoSQL database)
• Sqoop (relational database connector)
• HCatalog (table and storage management for Hive metastore)
• Kinesis Connector (directly access Kinesis streams in your scripts)
• Tachyon (accelerator for Spark)
• Derby (open-source relational DB in Java)
• Ranger (data security manager for Hadoop)
• Install whatever you want

## EMR Operations

### Usage

• Frameworks and applications are specified at cluster launch
• Connect directly to master to run jobs directly
• Or, submit ordered steps via the console
  • Process data in S3 or HDFS
  • Output data to S3 or somewhere
  • Once defined, steps can be invoked via the console

### Transient vs Long Running Cluster

* Transient Cluster
  * The total number of hours per day processing is less than 24
  * Not using HDFS as your primary data storage (instead you are using EMRFS with S3)
  * Job is intensive, interactive data
  * Can add multiple steps
  * Example:
    * Create 
    * Create a step that runs a Hive Script
    * The Hive script (Stored in S3) reads the log data stored in S3
    * Output results into an S3 bucket
    * Terminate
* Long Running
  * Frequently processing jobs
  * Processing jobs have input/output dependency on one another
  * Cost Effective store your data on HDFS instead S3
  * Requirement of higher performance I/O HDFs provides
  * Basically a data warehouse with periodic processing on large datasets
  * Termination protection on by default, auto-termination off

### Instances Types

* Primary Node
  * Clusters <= 50 Nodes
    * M5
  * Clusters > 50 Nodes
    * M4
* Core and Tasks Nodes
  * General Purpose -> M5, M4
  * Batch Processing, HPC, CPU-based machine learning -> C4,C5, Z1
  * Graphics processing on cpu based machine learning -> G2, G3, G4, P2 or P3
  * Spark Applications in memory caching -> R4 or R5
  * Large HDFS and Map reduce jobs requiring high I/O performance and high IOPS -> H1, I2, I3, or D2
* Spot instances
  • Good choice for task nodes
  • Only use on core & master if you’re testing or very cost-sensitive; you’re risking partial data loss

### Choosing Right Number of Instances

* Core Nodes and Task nodes - Depends
  * Run Taks (processing)
  * Store data (HDFS)
    * EMRFS
    * HDFS (Instance Store)
    * HDFS (EBS)
    * HDFS (Instance Store + EBS)

* Primary Node
  * One Instances or 3 Instances
* Change Replication Factor in rdfs-site.xml
  * Replication Factor
  * 10+ Instances -> replication factor is 3
  * 4-9 Instances -> replication factor is 2
  * 1-3 Instances -> replication factor is 1
  * Calculate HDFS Capacity
    * Number nodes x Storage Capacity / replication factor
* AWS Recommend
  * Smaller Cluster Large Nodes
    * Reduce Failure possibility
    * Reduces the amount of maintenance

### Choose Price Model

* Scenario
  * Long Running -> Cluster that are always running and have predictable variations in compute Capacity, such as a data warehouse
    * Primary Node -> On-demand
    * Core Nodes -> On-Demand / Instance Fleet
    * Task Nodes -> Spot or Instance Fleet
  * Cost Driven -> You are running transient cluster because lower cost is more important that time to complete
    * Primary Node -> Spot
    * Core Nodes -> Spot
    * Task Nodes -> Spot
  * Data Critical - Loosing partial work is not acceptable. Data must be persisted in HDFS and protected from sudden termination
    * Primary Node -> On-demand
    * Core Nodes -> On-Demand
    * Task Nodes -> Spot or Instance Fleet
  * Application Testing => When you are testing new applications to prepare production enviornments
    * Primary Node -> Spot
    * Core Nodes -> Spot
    * Task Nodes -> Spot

### Monitoring - Cloudwatch Metrics

* Cloudwatch Event Rule
  * Types
    * State Change
      * Auto Scaling Policy State Changes
      * Step Status Change
      * Cluster State Change
      * Instance Group State Change
      * Instance Group State Change Notification
    * EMR Configuration Error

* Tracking Cluster Progress
  * Running Map Tasks
  * Remaining Map Tasks
  * Remaining Reduce Tasks
* Detecting Idle Clusters
  * IsIdle
* No More Storage for HDFS
  * HDFSUtilization (%)

* Enable UI For EMR
  * This runs on Master
  * Using SSH tunneling with local port forwarding
  * Using SSH tunneling with dynamic port forwarding with socks proxy settings

* Resize Cluster
  * Can't have less core nodes than the replication factor
    * Change replication factor in hdfs-site.xml and restart named node daemon

### Scaling

* EMR Managed Scaling
  * Managed Scaling (Automatic Scale in/out)
  * MAX and Min core and task nodes
  * On demand limit
  * Maximum core
  * No Policy required
  * Supports Instance Groups or Instance Fleets
  * Evaluation every 5 to 10 seconds
  * Supported Applications: Only Yarn applications. Such as Spark, Hadoop, Hive, Flink. Presto is not supported
* Custom Auto Scaling
  * Scale In and Scale Out is based in CW Metrics
  * Needs create Scaling Policy and rules
  * Instance Groups only
  * Only in five minutes increments
  * Can choose which applications are supported when defining the automatic scaling rules

## File Storage And Comprehension

* Hadoop splits files per chunks
  * Each chunk is assigned map task
  * HDFS Files
    * Files are split into chunks automatically
  * S3
    * Files are split in multiple http range requests

* Comprehension Algorithm (Is important choosing one with splitting)
  * GZIP Algo
    * No Split
    * High Ratio
    * Medium Speed
    * File Sizes: 1-2 GB
  * bzip2
    * Split
    * Very high Ratio
    * Slow Speed
    * File Sizes: 2-4 GB
  * LZO
    * Split
    * Low ratio
    * Fast speed
    * File Sizes: 2-4 GB
  * Snappy
    * No Split
    * Low Ratio
    * Very fast speed
    * File Sizes 1-2 Gb

* File Formats
  * Text
    * CSG, TSV, JSON
    * Used Everywhere
  * Parquet
    * Columnar-oriented file format
    * Common used in the Hadoop ecosystem (Hive, HBase, MapReduce, Pig Spark)
    * Can increase performance
  * ORC
    * Optimized Row Columnar file format
    * Highly efficient way to store hive format
  * SequenceFile
    * Flat file consisting of binary key/value pairs
    * Used extensively with MapReduce input/output formats
  * Avro
    * Row-Oriented Data serialization framework
    * Develop and used by Hadoop project
    * Splitable

* Options to Smaller Files
  * Avoid smaller files (100 Mb or less), plan for fewer larger files
  * Typically you want files that map to your block size
  * Reduce HDFS block Size e.g. 1Mb (default is 128 Mb)
  * Use S3 DistCp to combine smaller files together (Extension of DistCP)
    * Combine small files into larger files (S3, HDFS)
    * Run S3 DistCP as a step or on primary node

## Security

* Two security groups are setup. One for primary node, and one for the secondary nodes (core and task nodes)
* These security groups only allow communicaton between each other. There is no outside traffic allowed by default 
* Need to setup Key Pair to SSH Access

• IAM policies 
   • Grant or deny permissions 
   • Allow user actions 
   • Combine with tagging to control access per cluster
• Kerberos 
  • Secure user authentication 
• SSH
  • Secure connection to command line 
  • Tunneling for web interfaces 
  • Can use Kerberos or EC2 key pairs 
• IAM roles 
  • Control access to EMRFS data based on user, group, location of data
  • Each cluster must have a service role and a role for the EC2 instance profile
  • IAM policies attached to roles 
  • Auto-scaling role 
  • Service-linked roles
• “Block public access” 
  • Easy way to prevent public access to data stored on your EMR cluster
  • Can set at the account level before creating the cluster.

## Configure Custom Hadoop settings

* Pre-Defined bootstrap actions to configure on cluster startup
* Can apply these in the console, cli, sdk by shorthand syntax on config object in json

## Extra Info

* Run Steps
  * Can use command-runner.jar -> Pre Installed script that permits several thinks like submit spark job or s3-dist-cp to copy data from S3 to EMR

## Reliability Considerations

* Disaster Recovery
  * Store your metadata outside the Cluster
    * Use AWS Glue Or
    * Run hive metastore on multi az RDS cluster
  * Spread clusters across AZs
* Failure Management
  * Keep data and applications on S3
  * Leverage EMR Multi Master
  * Design clusters to be transient, even if they are long-running
* Change Management
  * Provision resources in an automated way: Cloudformation, terraform
  * Ensure that all your code, scripts and other artifacts exist within source control

## Automate Resource Provisioning and Jobs Submission

* Configure a cluster and launch a job using step api
* Use Lambda to launch clusters using the EMR Step api
* Use an orchestration system to launch clusters and jobs (Step functions)
* Create a pipeline to schedule cluster creation and job scheduling (AWS Data Pipeline)

## Scale Up Cluster with Spot Instances

* On Demand Nodes for Worst case
* Spot Nodes to Save Cost
  * Adding spot nodes, the jobs will finished earlier and save costs

## Encryption

* KMS Encryption
* LUKS Encryption
* SSE-KMS
  * Data in S3
* CSE-KMS
  * EMR First encrypts the data with a CMK, then sends to S3 for storage
  * Data in S3
* SSE-S3
  * Data in S3
* EMR Don't Support SSE-C

# Redshift

* Use When Have
  * Large Amounts of highly structured data
  * Frequency of access
  * Access and usage pattern

* Dblink allows you to offload queries in Redshift to another database entirely.
  * Connect Redshift to PostgreSQL (possibly in RDS)
  * Good way to copy and sync data between PostgreSQL and Redshift
* Enhanced VPC Routing forces Redshift to use the VPC for all COPY and UNLOAD commands, which in turn will help us make sure that all that traffic appears on the VPC Flow logs.

## Architecture

* Leader Node
  * Schema Management
  * Warehouse Metadata
  * Query Planning
* Worker Node
  * Query Execution
  * Slice Management

## Nodes Types

* Dense Compute
  * Node Size
    * dc2.large
      * Default Slices: 2
      * Storage: 160 GB NVMe-SSD
      * Total Capacity: 5.12 TB
    * dc2.8xlarge
      * Default Slices: 16
      * Storage: 256 GB NVMe-SSD
      * Total Capacity: 326 TB
  * Use Cases: Real Time Queries. Queries very fast
* Dense Storage
  * Node Size
    * ds2.xlarge
      * Default Slices: 2
      * Storage 2 TB HDD
      * Total Capacity: 64 TB
    * ds2.8xlarge
      * Default Slices: 16
      * Storage 16 TB HDD
      * Total Capacity: 2 PB
  * Use Cases: Huge amount data
* RA3
  * Storage backed by S3 and uses instance store to provide hot data to queries
  * ra3.4xlarge
    * Default Slices: 4
    * Storage: 64 TB
    * Total Capacity: 4096 TB
  * ra3.16xlarge
    * Default Slices: 16
    * Storage: 64 TB
    * Total Capacity: 8192 PB

### Slice

* Each slice have their own separate cpu memory, storage
* Receives Query script
* Executes Script
* Returns Results

### Query Process

* Client Does query to Leader Node
* Leader Node generates query plan (group of execution scripts)
* Execution scripts go to worker nodes and slices execute and returns the results
* Leader aggregates the results and send query response

## Use Cases

* Data Warehouse vs Versus Data Lake
  * Data Warehouse is:
    * Structure
    * Cataloging
    * Access Speed
* Use Cases
  * User Access Logs
  * Transaction Artifacts
  * API Error/Access Logs
  * Email Artifacts
  * Lambda Logs
  * Data Mutation
  * Data Stream Metadata
  * General Application Metadata
  * Financial Analytics
  * Machine Learning

## Table Design

* Comprehension
  * ANALYZE COMPRESSION is built-in command that will find the optimal compression for each column on an existing tabl
    * Give you the smallest, not the best performance
  * We can Compress individual columns
  * Change column encoding requires table rebuild
  * Raw Encoding
    * Default
      * Sort Keys (Cannot be compreension)
      * Boolean
      * Real, Double
    * Uses for sparse columns and small tables
  * AZ64
    * Default
      * SMALLINT
      * Integer
      * BigInt
      * Decimal
      * Date
      * Timestamp
      * Timestamptz
  * Byte Dicticionary
  * Delta
  * LZO
    * Char and VarChar
  * Mostlyn
  * Run-length
  * Text
  * Zstandard

* Sort Keys
  * Single Column 
  * Compound
    * Default
    * By Operations (SORT, Order, Partition by, GROUP BY )
    * Multi-column sorting, Hierarchical Data
  * Interleaved
    * Increase Table Maintenance
    * Best for where predicates
    * Single Column Key
    * Best when queries depends on Secondary Sort Keys
    * Gives equal weight to each column in the sort key.
  * Best Pratices
    * Use on Columns that are frequently filtered on placing the lowers cardinality columns first
    * On most fact tables, the first sort key column should be a temporal column
    * Columns added to a sort key after high-cardinality column are not effective
    * Sort Keys are less beneficial on small tables
    * Define four or less sort Keys columns -- more will result in marginal gains and increased ingestion overhead

* Distribution Styles
  * Even
    * Blocks are distributed evenly between cluster slices (Default)
    * Round robin
    * if neither KEY or ALL Apply
    * Rows distributed across slices in round-robin
  * Key
    * Identical Key Values are stored on same slice
    * Value is hashed
    * Rows distributed based on one column
    * Summary
      * Optimize JOIN Performance between large tables by distributing on colummns used in ON clause
      * Optimize INSERT INTO Select performance
      * Optimize GROUP By performance
      * The column that is being distributed on should be have high cardinality and not cause row skew
  * All
    * Full table data goes to the first slice of every node
    * use typically in small tables (dimension tables)
    * Summary
      * Optimize JOIN Performance with dimension tables
      * Reduces disk usage on small tables
        * Small and medium size dimension tables (< 3M Rows)
  * Auto
    * Combines EVEN and ALL
    * If the table is small uses ALL, when grows up and reach a threshold, will be converted in even
    * Default
    * Redshift figures it out based on size of dat


* Constraints
  * Primary, Foreign Key
    * Used by the query planner as a hint about relations
  * Unique
    * Not enforced, used by query planner as a hint
  * Not Null
    * Not enforced, or respected

## Redshift Spectrum

* Used to create foreign tables from data stored in S3
* Uses EXTERNAL Keyword for schema and table creation
* Supports SELECT and INSERT operations
* UPDATE and DELETE are not supported -> These operations need to be done outside redshift spectrum

* Access Control
  * IAM
  * AWS Lake Formation to more granular permissions

* Data Store -> Made of
  * GLUE Data Catalog
  * Athena
  * S3 Data Bucket

* Can be used to data Ingestion in Redshift
  * Use INSERT INTO Select from external S3 tables
    * Load data in alternative file formats: Amazon ION, Grok, RCFile, and Sequence
  * Best Practices
    * Save cluster resources for querying and reporting rather than ELT
    * Filtering/aggregation incoming data can improve performance over COPY
  * Considerations
    * Repeated reads against S3 are not transactional

• Query exabytes of unstructured data in S3 without loading
• Limitless concurrency 
• Horizontal scaling 
• Separate storage & compute resources
• Wide variety of data formats 
• Support of Gzip and Snappy compression

## Resize Cluster

* Classic Resize
  * Source Cluster -> Enters in Read Only Mode
  * Move User Schemas, User Tables, User Data
  * End it's Done, move cluster Endpoint to Resized Cluster
  * Hours to Days
  * Change Node Type
  * Snapshot, Restore, Resize
    * Used to keep cluster available during a classic resize
    * Copy cluster, resize new cluster
  * Duration Impacting Factors
    * Source cluster activity
    * Size and number of tables
    * Uniformity of data distribution across nodes
    * Source and Target node configuration
  * Use Cases
    * Change cluster instance type (SSD to HDD)
    * If elastic resize is not an option because of sizing limits
* Elastic Resize
  * Creates Cluster Snapshot  -> Enters in Read Only Mode
  * Create Resized cluster from cluster snapshot
  * move cluster Endpoint to Resized Cluster
  * Nodes are added/remove to/from existing cluster
  * Minutes
  * Inflight queries/connections are put on hold
  * Some queries within transactions maybe rollback
  * Cluster is down for a few minutes
  * Tries to keep connections open across the downtime
  * Constraints
    * Can't be used on single node clusters
    * Cluster must be in a VPC
    * New configuration must have sufficient storage
    * Can schedule the resize based at time or recurring events
    * Check available resize configurations
      * describe-node-configuration-options
    * Can change node type but not without dropping connections -> creates a whole new cluster
    * Limited to doubling or halving for some dc2 and ra3 node types.
  * Use Cases
    * Scale up and down for workload spikes
    * Incrementally add/remove storage

* Best Pratices
  * Use at least two computes nodes (multi-node cluster) in production for data mirroring
    * Leader node is given for no additional cost
  * Maintain at least 20% free space or three times the size of the largest table
    * Scratch space for usage, rewiriting tables
    * Free space is required for vacuum to re-sort table
    * Temporary tables used for intermediate query results
  * The maximum number of available Redshift Spectrum nodes is a function of the number of slices in Redshift
  * If are using DS2 migrate to RA3
  * If using DC1, migrate to DC2

## Scaling

* Vertical and horizontal scaling on demand
• During scaling: 
   • A new cluster is created while your old one remains available for reads
   • CNAME is flipped to new cluster (a few minutes of downtime)
   • Data moved in parallel to new compute nodes

## Vacuum And Deep Copy

* Vaccum
  * Vacuum Full
    * Reclaim Disk Space
    * Sort Table
    * Reindex Table
      * Interleaved tables require explicit VACUUM REINDEX
    * Use Cases
      * Useful to maintenance period
      * Lots disk to reclaim
      * Lots unsorted rows
  * VACCUM SORT ONLY
    * Sort Rows
    * Default threshold 95% Sorted
  * VACCUM DELETE ONLY
    * Reclaims Disk Space
    * Default threshold 5% marked for deletion
  * VACCUM REINDEX table_name
    * Performs VACCUM FULL on
    * Interleaved Tables

  * Can Set TO threshold PERCENT to perform SORT, DELETE Operations
  * BOOST
    * Ignores cluster activity and contends for resources like other processes
    * Use in maintenance Window
  * Automatic Vaccum Delete
    * Triggered by high percentage of rows marked for deleting
      * Activity monitoring dictates schedule
      * Automatically reclaims disk space
    * Automatic Table sort
      * Triggered by a high percentage of unsorted rows
      * Utilizes SCAN operations to identify unsorted tables
  * Automatic Analyze
    * Automatically updates table statistics
    * Waits for low activity periods to run analyze jobs
    * Utilizes table statistic age for triggering

* Deep Copy
  * Create New Table
  * Copy Data
    * Delete resources marked for deletion
  * Truncate Source
  * Rename Target
  * Requires the table not being used
  * Can be less time than full vaccum
  * Methods
    * Use Original Table DDL
    * Create Table Like
    * Temp Table, Truncate
      * Create Temp Table, Copy Data
      * Truncate Source
      * Copy Data
      * Drop Temp Table

* Analyze
  * Use to collects table statistics for optimal query planning
  * in the vast majority of cases AUTO Analyze automatically handles statistics gathering
  * Best Pratices
    * ANALYZE can be run periodically after ingestion on just the colummns that where predicates are filtered on

## Backup And Restore

* Snapshots
  * Point in time backup of whole cluster
  * Can be manually triggered
  * Scheduled automated snapshots
  * Can disable at table level
  * Asynchronously replicated to another region
  * Restoring from a snapshot
    * Creates a new cluster
    * Snapshot data is lazy loaded as queries request it
* Loading Data from S3
  * Copy command identifies target table
  * From directive identifies source file
  * iam_role identifies role with s3 read permissions
  * Redshift retrieves and processes file from s3
  * Must be same Region??
  * COPY Command
    * Number of input files should be an multiple of the number of slices
    * Parallelized; efficient
    * From S3, EMR, DynamoDB, remote hosts
    * S3 requires a manifest file and IAM role
    * Splitting the single file into 16 (number of slices) input files, all slices are working to maximize ingestion performance
    * COPY continues to scale linearly as you add nodes
    * Recommendations
      * Delimited files are recommended -- 1MB to 1GB after gzip compression
      * The Files should be the same size to optimal performance
      * PICK simple delimiter, PICK simple NULL Character (\N)
      * Use double quotes and escape character for varchars
      * UTF8 varchar columns take four bytes per char
* Unloading data to S3
  * Unload command identifies data to unload
  * To directive identifies target bucket
  * iam_role identifies role with S3 write permissions
  * Format directive specifies the target file format

* Use COPY to load large amounts of data from outside of Redshift
• If your data is already in Redshift in another table, 
     • Use INSERT INTO …SELECT 
     • Or CREATE TABLE AS 
* • COPY can decrypt data as it is loaded from S3 
  * • Hardware-accelerated SSL used to keep it fast 
• Gzip, lzop, and bzip2 compression supported to speed it up further
• Automatic compression option 
  * • Analyzes data being loaded and figures out optimal compression scheme for storing it
  
• Special case: narrow tables (lots of rows, few columns) 
   • Load with a single COPY transaction if possible 
   • Otherwise hidden metadata columns consume too much space

## Monitoring

* Cluster
  * CPU Utilization
  * Maintenance Mode
* Storage
  * Percentage Disk Space Used
  * Auto Vacuum space Freed
  * Read Throughput
  * Read Latency
  * Write Throughput
  * Write Latency
* Database
  * Database Connections
  * Total Table Count
  * Health Status
* Queries Load
  * Query Duration
  * Query Thoughput
  * Query Duration per WLM queue
  * Concurrency Scaling Activity
  * Concurrency Scaling Usage
  * Average Query Queue Time by Priority
* Usage Limits
  * Usage Limit For Concurrency Scaling
  * Usage Limit for Redshift Spectrum

## Performance

• Massively Parallel Processing (MPP) 
• Columnar Data Storage 
• Column Compression

## Deduplication/Upsert

Create transaction with this process

* Load CSV data into a staging table
* Delete duplicate data from the production table
* Insert (or append) data from the staging into the production table

## ELT Best Practices

* Wrap workflow/statements in an explicit transaction
* Consider using DROP TABLe or TRUNCATE instead of delete
* Staging Tables:
  * If possible use DISTSTYLE KEY on both the staging table and production table to speed up the INSERT INTO Select statement
  * With COPY turn of automatic compression - COMPUDATE OFF
  * Copy compression settings from the production table (using like keyword) or manually apply compression to CREATE TABLE DDL (from ANALYZE COMPRESSION output)
  * For copying a large number of rows (> hundreds of millions) consider using ALTER TABLE APPEND instead of INSERT INTO SELECT

## WLM

* Allows for the separation of different query workloads
* Prioritize important queries
  * Prioritize short, fast queries vs. long, slow queries
* Throttle/abort less important queries
* Control concurrent number of executing queries
* Divide cluster memory
* Set query timeouts to abort long running queries
* Queue
  * Assigned a percentage of cluster memory
  * SQL Queries execute in queue based on
    * User Group: which groups the user belongs to
    * Query Group Session level variable
* Query Slots (or Concurrency)
  * Division of memory within a WLM queue, correlated with the number of simultaneous running queries
  * WLM_QUERY_SLOT_COUNT is a session level variable
    * Usefull to increase for memory intensive operations like (COPY, VACUUM, large insert into SELECT)
* Short Query Acceleration (SQA)
  * Automatically detect short running queries and run them within the short query queue if queuing occurs
  * Can be enable/disable
  * Works with:
    * • CREATE TABLE AS (CTAS)
    * Read-only queries (SELECT statements)
  * Can configure how many seconds is “short"
* Concurrency Scaling
  * Enabled by queue 
  * When queues are full, queries are routed to transient Redshift clusters (added in seconds)
  * Automatically adds cluster capacity to handle increase in concurrent read queries
  • Support virtually unlimited concurrent users & queries
  • WLM queues manage which queries are sent to the concurrency scaling clusteR
* Super Admin Queue
  * Hidden from console, can be used by admins, manually switched in session
  * Has single slot the equivalent of 5-7% memory allocation and no timeout
  * Not use for long running queries
* Queue Attributes
  * Percent Memory
  * concurrency/queue slots
  * Concurrency scaling
  * query timeout
  * Enable short query acceleration

* Auto WLM
  * Allows for prioritization of different query workload
  * Goals -> Simplify WLM
  * Automatically controls concurrent number of executing queries
  * Automatically divides cluster memory
  * Creates up to 8 queues
  * Default 5 queues with even memory allocation
  * Large queries (ie big hash joins)->concurrency lowered
  * Small queries (ie inserts, scans, aggregations)-> concurrency raised
  * Configuring query queues 
    * • Priority 
    * • Concurrency scaling mode 
    * • User groups 
    * • Query groups 
    * • Query monitoring rules

* Query Monitoring Rules (QMR)
  * Extension of workload management(WLM)
  * Allow the automatic handling of runaway (poorly written) queries
  * Rules applied to a WLM queue allow queries to be
    * LOGGED
    * ABORTED
    * HOPPED
  * Goals
    * Protect against wasteful use of cluster
    * Log resource-intensive queries

* Best Pratices
  * Use Auto WLM - if you aren't sure how to set up WLM or your workload is highly unpredictable, or you are using the old default WLM
  * Use manual WLM if you understand your workload patterns or require throttling certain types of queries depending on the time of day
  * Keep the number of WLM queues to a minimum, typically just three queues to avoid having unused queues
  * USE WLM to limit ingestion/ELT concurrency to two to three
  * To maximize query throughput use WLM to throttle the number of concurrent queries to 15 or less
  * use QMR rather than WLM to set query timeouts
  * use QMR to log long-running queries
  * Save the superuser queue for administration tasks anc canceling queries

## Redshift Advisor

* Recommendations
  * Skip compression analysis during COPY
  * Split S3 Objects loaded by COPY
  * Compress S3 objects loaded by COPY
  * Compress table data
  * Reallocate WLM memory
  * Cost Savings
  * Alter distributions keys on tables

## Sizing Cluster for production

* Estimate the uncompressed size of incoming data
* Assume 3x compression (actual can be 4x)
* Target 30-40% free space (resize to add/remove storage as needed)
  * Disk utilization should be at least 15% and less than 80%
* Based on performance requirements, pick SSH or HDD
  * if required, additional nodes can be added for increased performance

## Security

• Using a Hardware Security Module (HSM)
   • Must use a client and server certificate to configure a trusted connection between Redshift and the HSM
   • If migrating an unencrypted cluster to an HSM-encrypted cluster, you must create the new encrypted cluster and then move data to it.
• Defining access privileges for user or group
    • Use the GRANT or REVOKE commands in SQL
    • Example: grant select on table foo to bob

# AWS GLUE

* AWS Serverless ETL
* Categorize, clean, enrich your data
* Move data between various data stores

## Use Cases

* Query Data in S3
* Joining data for a data warehouse
* Creating a centralized data catalog
* Supports schema conversion (Semi structure to relational)

## Components

* Sources
  * S3, DynamoDb, RDS, Redshift, Database Ec2
* Crawler
  * Crawls the databases and store metadata in data catalog
* Job
  * Runs in data catalog can be scala / python
* Output
  * Athena, S3, EMR, Redshift

## Crawler

• Glue crawler scans data in S3, creates schema
• Can run periodically
• Populates the Glue Data Catalog
  • Stores only table definition
  • Original data stays in S3
* Use Resource Policies to access only to one table

## Data Catalog

* Persistent metadata store
* Can store, annotate and store metadata between AWs Services (Similiar to Apache Hive)
* Centralized Repository
* Provided compheensive autdit

## S3 Partitions

• Glue crawler will extract partitions based on how your S3 data is organized
• Think up front about how you will be querying your data lake in S3
• Example: devices send sensor data every hour
• Do you query primarily by time ranges?
  • If so, organize your buckets as yyyy/mm/dd/device
• Do you query primarily by device?
  • If so, organize your buckets as device/yyyy/mm/dd

## Glue + Hive

• Hive lets you run SQL -like queries from EMR
• The Glue Data Catalog can serve as a Hive “metastore ”
• You can also import a Hive metastore into Glue

## Glue Job

* Automatic code gener
* Input: Data Catalog (Data Source)
* Job Processing: Scala / Python
* Transform data, Clean Data, Enrich Data (before doing analysis)
  • Generate ETL code in Python or Scala, you can modify the code
  • Can provide your own Spark or PySpark scripts
  • Target can be S3, JDBC (RDS, Redshift), or in Glue Data Catalog
• Fully managed, cost effective, pay only for the resources consumed
• Jobs are run on a serverless Spark platform
• Glue Scheduler to schedule the jobs (Schedule at minimum 5 minutes)
• Glue Triggers to automate job runs based on “events”
* Trigger: On Demand / Schedule / Event
* Outputs File Formats
  * JSON => optional comprehension (gzip, bzip2)
  * CSV => optional comprehension (gzip, bzip2)
  * ORC
  * Parquet
  * AVRO
* DPUS
  * Use CW Metrics to determine under or over provisioned dpus in the cluster by monitoring the total number of actively running executors
  * Apache Spark
    * Minimum DPUs: 2
    * Maximum DPUs: 100
    * Default DPUs: 10
  * Spark Streaming
    * Minimum DPUs: 2
    * Maximum DPUs: 100
    * Default DPUs: 5
  * Python Shell
    * Minimum DPUs: 0.0625 or 1
    * Maximum DPUs: 1
    * Default DPUs: 0.00625
* What Glue Job Needs
  * Provide input data sources and output targets in your vpc
  * in addittion you provide IAM role, VPC ID, subnet ID and SG that are needed to access data sources and targets
* Traffic is Governed by your VPC
  * Traffic in, out and within the spark environment is determined by your networking policies
  * The one exception are calls made the AWS GLUE API. Can be audited via Cloudtrail
* Can import Custom python libraries and Jar Files
* Encryption
  • Server-side (at rest)
  • SSL (in transit)
* • Errors reported to CloudWatch
    • Could tie into SNS for notification
* DynamicFrame
* • A DynamicFrame is a collection of DynamicRecords
  • DynamicRecords are self-describing, have a schema
  • Very much like a Spark DataFrame, but with more ETL stuff
  • Scala and Python APIs
* Transformations
  • Bundled Transformations:
     • DropFields, DropNullFields – remove (null) fields
     • Filter – specify a function to filter records
     • Join – to enrich data
     • Map - add fields, delete fields, perform external lookups
  • Machine Learning Transformations:
     • FindMatches ML: identify duplicate or matching records in your dataset, even when the records do not have a common unique identifier and no fields match exactly.
  • Format conversions: CSV, JSON, Avro, Parquet, ORC, XML
  • Apache Spark transformations (example: K-Means)
    • Can convert between Spark DataFrame and Glue DynamicFrame
* ResolveChoice
  • Deals with ambiguities in a DynamicFrame and returns a new one
  • For example, two fields with the same name.
  • make_cols: creates a new column for each type
      • price_double, price_string
  • cast: casts all values to specified type
  • make_struct: Creates a structure that contains each data type
  • project: Projects every type to a given type, for example project:string
* Modifying the Data Catalog
* • ETL scripts can update your schema and partitions if necessary
  • Adding new partitions
     • Re-run the crawler, or
     • Have the script use enableUpdateCatalog and partitionKeys options
  • Updating table schema
     • Re-run the crawler, or
     • Use enableUpdateCatalog / updateBehavior from script
  • Creating new tables
     • enableUpdateCatalog / updateBehavior with setCatalogInfo
  • Restrictions
     • S3 only
     • Json, csv, avro, parquet only
     • Parquet requires special code
     • Nested schemas are not supported
* Development Endpoints
* • Develop ETL scripts using a notebook
     • Then create an ETL job that runs your script (using Spark and Glue)
  • Endpoint is in a VPC controlled by security groups, connect via:
     • Apache Zeppelin on your local machine
     • Zeppelin notebook server on EC2 (via Glue console)
     • SageMaker notebook
     • Terminal window
     • PyCharm professional edition
     • Use Elastic IP’s to access a private endpoint address
* CloudWatch Events 
  • Fire off a Lambda function or SNS notification when ETL succeeds or fails
  • Invoke EC2 run, send event to Kinesis, activate a Step Function
* Anti-patterns
  • Multiple ETL engines 
    • Glue ETL is based on Spark 
    • If you want to use other engines (Hive, Pig, etc) Data Pipeline EMR would be a better fit.
* GLUE ETL Supports serverless steraming ETL
  • Consumes from Kinesis or Kafka
  • Clean & transform in-flight
  • Store results into S3 or other data stores
  * Runs on Apache Spark Structured Streaming

## Glue Studio 

• Visual interface for ETL workflows
• Visual job editor 
   • Create DAG’s for complex workflows
   • Sources include S3, Kinesis, Kafka, JDBC
   • Transform / sample / join data • Target to S3 or Glue Data Catalog
   • Support partitioning 
• Visual job dashboard 
   • Overviews, status, run times

## Glue DataBrew 

• A visual data preparation tool 
   • UI for pre-processing large data sets 
   • Input from S3, data warehouse, or database 
   • Output to S3 
• Over 250 ready-made transformations 
• You create “recipes” of transformations that can be saved as jobs within a larger project
• Security 
   • Can integrate with KMS (with customer master keys only)
   • SSL in transit • IAM can restrict who can do what • CloudWatch & CloudTrail

## Glue Elastic Views

• Builds materialized views from Aurora, RDS, DynamoDB
• Those views can be used by Redshift, Elasticsearch, S3, DynamoDB, Aurora, RDS
• SQL interface 
• Handles any copying or combining / replicating data needed
• Monitors for changes and continuously updates
• Serverless

## Job Bookmarks

* If enabled, glue job knows which the files already ran
* Way to process new data without reprocessing old data
* The persistent state and information that AWS GLUE tracks frm previously ran jobs
* You can process new data when re running jobs on scheduled interval
* Works with S3 sources in a variety of formats 
* Works with relational databases via JDBC (if PK’s are in sequential order) 
    • Only handles new rows, not updated rows
* States
  * Enabled
    * Causes the job to update the state after a run to keep track of previously processed data. If new data is processed, it will start from the last checkpoint
  * Disables
    * Job Bookmarks are not used, and the job always processes the entire dataset. This is default
  * Pause
    * Processes incremental data defined by a from value and to-value range. The data that is after from-value job and before to value job (inclusive)

## Costs

* Monthly fee for storing and acessing the metadata in the Glue Data Catalog
* An hourly rate (billed per second) for the crawler with 10-minute minimum
* An hourly rate (billed per second) for developer endpoints with 10-minute minimum
* An hourly rate (billed per second) for ETL jobs with either 1-minute or 10-minute minimum based on Glue version selected

# Lake Formation

• “Makes it easy to set up a secure data lake in days”
• Loading data & monitoring data flows 
• Setting up partitions 
• Encryption & managing keys 
• Defining transformation jobs & monitoring them
• Access control 
• Auditing 
• Built on top of Glue

* Troubleshooting
 • Cross-account Lake Formation permission
   • Recipient must be set up as a data lake administrator
   • Can use AWS Resource Access Manager for accounts external to your organization
   • IAM permissions for cross-account access
 • Lake Formation does not support manifests in Athena or Redshift queries
 • IAM permissions on the KMS encryption key are needed for encrypted data catalogs in Lake Formation
 • IAM permissions needed to create blueprints and workflows

# ATHENA

* presto under hood
* Data Formats
  * CSV
  * JSON
  * TSV
  * TextFiles
  * ORC (columnar, splittable)
  * Parquet (columnar, splittable)
  * Avro (splittable)
* Compreenshions
  * Snappy
  * ZLIb
  * LZO
  * GZIP
* Service Integrations
  * Quicksight
  * GLUE

## Data Sources

* S3
* Data Source (Athena Federated Queries)
  * Configure connector for common data sources
  * Supported
    * CW Logs
    * CW Metrics
    * DocumentDB
    * DynamoDB
    * Redshift
    * HBase
    * MySql
    * Redis
    * Postgres
  * Query result stored in S3

## Integrations

* Quicksight
* Glue 
* External Hive metastore
  * Necessary lambda to do integration
* Cloudtrail
* Cloudfront
* ELB
* VPC FLOW Logs
* Systems Manager Inventory
* Athena comes with OBDC and JDBC driver that you can use with other BI TOOLS and SQL clients
* Integration with Jupyter, Zeppelin, RStudio notebooks

## When Use

* Similar Services
  * Redshift
    * Fast querying, enterprise reporting, and BI workloads
    * Very complex SQL (multiple joins and sub-queries)
    * Bringing data from many different data sources a common format
    * Storing data for long periods of time
    * Build sophisticated business reports from historical data
  * EMR
    * Simple to run distributed processing frameworks like Hadoop, Spark, and Presto
    * Flexible enough to run custom applications and code
    * You define the compure, memory and storage to optimize workloads
  * Athena
    * Easily run ad-hoc queries for data in S3
    * No need to setup of manage servers
    * No need to format data
    * Can be used with EMR and redshift as an integrated Data catalog
  * S3 Select
    * Retrieves a subset of data from an object
    * Offload filtering of data in S3 instead of your application
    * Simple SQL expressions
    * Limited data formats: CSV, JSON, Parquet (GZIP and BZIP2 for CSV and JSON)
  * S3 Glacier Select
    * Queries Glacier data within minutes
    * Use standard SQL statements
    * No need to restore to S3
    * you can’t directly query a GZIP-compressed CSV file using Amazon S3 Glacier Select

    
## Data partition

* By partitioning your data, you can restrict the amount of data scanned by each query
* Can partition your data on any key with common practice to partition data based on time
* Leading to multi-level partitioning schema (year, month, day, and hour)
* You can use the partitioned by clause in the create table statement

## Cross-region concerns

• Athena cannot query across regions on its own 
  • BUT a Glue Crawler can 
  • So, you can query S3 data across regions if you query a Glue Data Catalog in the same region as Athena 
    • And, the Glue Crawler that created the data catalog spanned multiple regions.

## Workgroups

• Can organize users / teams / apps / workloads into Workgroups
• Can control query access and track costs by Workgroup
• Integrates with IAM, CloudWatch, SNS
• Each workgroup can have its own: 
  • Query history 
  • Data limits (you can limit how much data queries may scan by workgroup)
  • IAM policies 
  • Encryption settings

## Pricing

* Athena is priced per query and charges are based on the amount of data scanned by the query
* Compressing, partitioning, or using columnar formats allow you to pay less because you scan less data

## Performance

• Use columnar data (ORC, Parquet)
• Small number of large files performs better than large number of small files
• Use partitions
  • If adding partitions after the fact, use MSCK REPAIR TABLE command

# Amazon Quicksight

* Data
  * AWS Data
  * Applications
  * Files
  * Data preparation allows limited ETL
* Relational Data
  * Athena
  * Aurora
  * Redshift
  * Redshift Spectrum
  * S3
  * S3 Analytic
  * Iot Analytic
* Can Connect SaaS
  * Jira
  * ServiceNow
  * Github
  * Salesforce
  * Twitter
  * Adobe Analytics

## Spice

* Data load to spice (Super-Fast Parallel, in-memory calculation engine)
* Analytical queries process faster
* You do not need to wait for a direct query to process
* Data stored in SPICE can be reused multiple times without incurring additional costs
* The spice capacity is allocated separately for each Region

• Data sets are imported into SPICE • Super-fast, Parallel, In-memory Calculation Engine
  • Uses columnar storage, in -memory, machine code generation
  • Accelerates interactive queries on large datasets
• Each user gets 10GB of SPICE 
• Highly available / durable 
• Scales to hundreds of thousands of users 
• Can accelerate large queries that would time out in direct query mode (hitting Athena directly) 
  • But if it takes more than 30 minutes to import your data into SPICE it will still time out

## Visualization Type

* AutoGraph
* Bar Charts
  * For comparison and distribution (histograms)
* Combo Charts
* Donut Charts
  * Percentage of Total Amount
* Gauge Charts
* Area Line Chart
  * Trends over time
  * For changes over time
* Pivot Tables
  * Interactive exploration of multi-dimensional data, as long as you’re not looking to quickly identify outliers at the same time.
  * For tabular data
* KPI
* Heat Map and Scatter Plots
  * You can think of heat maps as pivot tables that highlight outliers and trends using color.
  * For correlation
* Pie graphs, tree maps 
  * • For aggregation

## Notes

* specify User access
  * when share a dhasboard, you specify which users have access to it
* Viewers vs owners
  * Viewers have limited access (View, filter, sort)
* Embedded in Website of Application
  * Only in Enterprise edition

## Data Encryption

* Encryption at Rest (Enterprise edition only)
  * All of the metadata and data uploaded into SPICE is encrypted with AWS-managed keys
* Encryption in Transit
  * Quicksight supports encryption for all data transfer using SSL
  * This includes data to and from SPICE to the user interface
* Key Management
  * AWS manages all of the keys associated with Quicksight
  * Database server certificates are the responsibility of the customer

## Connection to AWS Resources

* To: Redshift, RDS, Aurora, Database on EC2
  * Database username, password, port
  * Public/private resources
  * SGs, IGW, NAT GW, NACLs
  * Manual Connect
    * User must have SELECt Permissions
  * Auto Discovery
    * The resources must be in the same region as you Quicksight account
* To: S3
  * IAM Role
  * Bucket Policy
  * Manifest file (metadata file)

* Allowing Access to VPC Resources
  * For Amazon QuickSight to connect to a private VPC resource, the security group must contain an inbound rule authorizing access from the appropriate IP Address range for the Quicksight servers in that AWS Region

* Auth Integration
  * IAM
  * AWS Directory Service
    * Only Enterprise Edition
  * AWS Directory Service with AD Connector
    * Only Enterprise Edition
  * On-premise Active Directory with SSO or AD Connector
    * Only Enterprise Edition
  * SSO using AWS Single Sign-On Service or another third party federation service
    * Only Enterprise Edition
  * SSO through SAML 2.0

* Can register VPC connection in QuickSight

## Best Practice for Security

* Firewall
  * Allow access to HTTPS and WebSockets (wsss://) protocol. This allows Quicksight to reach a database that is not on AWS
    * Change The non-AWS servers firewall configuration to accept traffic from the appropriate QuickSight IP range
* SSL
  * Use SSL to connect to your database. Require use publicly-recognized certificate authorities
* Enhanced Security
  * Encrypt data stored at rest in SPICE
  * Integrate Active Directory and SSO authentication
  * Securely access data in private VPCs and on-premises
  * Limit access to data with low-level security
* VPC
  * Use Direct Connect to create a secure, private connection to link your on-premise resources of your VPC and QuickSight
  * • Add QuickSight’s IP address range to your database security groups

* Row -level security – Enterprise edition only

* Multi-factor authentication on your account

• Resource access 
  • Must ensure QuickSight is authorized to use Athena / S3 / your S3 buckets
  • This can be managed within the QuickSight console
• Data access 
  • Can create IAM policies to restrict what data in S3 given QuickSight users can access

* Quicksight + Redshift: Security
   • By default Quicksight can only access data stored IN THE SAME REGION as the one Quicksight is running within
   • So if Quicksight is running in one region, and Redshift in another, that’s a problem
   • A VPC configured to work across AWS regions won’t work!
   • Solution: create a new security group with an inbound rule authorizing access from the IP range of QuickSight servers in that region

* User Management
   • Users defined via IAM, or email signup
   • Active Directory connector with QuickSight Enterprise Edition
      • All keys are managed by AWS; you CANNOT use customer provided keys
      • Enterprise edition only!
      • Can tweak security access using IAM if needed

## Dashboards

• Read-only snapshots of an analysis
• Can share with others with Quicksight access
• Can share even more widely with embedded dashboards
   • Embed within an application
   • Authenticate with Active Directory / Cognito / SSO
   • QuickSight Javascript SDK / QuickSight API
   • Whitelist domains where embedding is allowed

* Is better using Highcharts or d3.js, Chart.js to visualize data on the web

## Machine Learning Insights

• ML-powered anomaly detection
   • Uses Random Cut Forest
   • Identify top contributors to significant changes in metrics
• ML-powered forecasting
  • Also uses Random Cut Forest
  • Detects seasonality and trends
  • Excludes outliers and imputes missing values
• Autonarratives
  • Adds “story of your data” to your dashboards
• Suggested Insights
  • “Insights” tab displays read-to-use suggested insight

* Quicksight Q
  • Machine learning -powered
  • Answers business questions with Natural Language Processing 
    • “What are the top-selling items in Florida?”
  • Offered as an add -on for given regions
    • Personal training on how to use it is required
    • Must set up topics associated with datasets 
         • Datasets and their fields must be NLP friendly
         • How to handle dates must be defined

# Opensearch

## Hierachy

* Index
  * Type -> Second Level, Used to categorize data
    * Document
  * State Management
    * Automates index management policies
    * Examples 
      * • Delete old indices after a period of time 
      * • Move indices into read only state after a period of time
        • Move indices from hot -> UltraWarm -> cold storage over time
        • Reduce replica count over time • Automate index snapshots
      * ISM policies are run every 30 -48 minutes
        • Random jitter to ensure they don’t all run at once
      • Can even send notifications when done
    * Index rollups
      * Periodically roll up old data into summarized indices
      • Saves storage costs 
      * New index may have fewer fields, coarser time buckets
    * Index Transforms
      * Like rollups, but purpose is to create a different view to analyze data differently.
      * Groupings and aggregations
* Horizontally scalable
   * Scale up or down without downtime • But this isn’t automatic

## Storage

• Standard data nodes use “hot” storage
   • Instance stores or EBS volumes / fastest performance
• UltraWarm (warm) storage uses S3 + caching
   • Best for indices with few writes (like log data / immutable data)
   • Slower performance but much lower cost
   • Must have a dedicated master node
• Cold storage
   • Also uses S3
   • Even cheaper
   • For “periodic research or forensic analysis on older data”
   • Must have dedicated master and have UltraWarm enabled too.
   • Not compatible with T2 or T3 instance types on data nodes
   • If using fine-grained access control, must map users to cold_manager role in OpenSearch Dashboards
• Data may be migrated between different storage types

## Service Integrations

* Kinesis Data Firehose
  * Elasticsearch service is configurable target
* Cloudwatch
  * Cloudwatch log subscription can deliver to Elasticsearch service
* IoT
  * IoT rules can send data to Elasticsearch

## Visualization tools Examples

* Kibana (Dashboards)
* D3js
  * Easily embedded in external applications
  * Can visualize any json formatted data
  * Third Party connectors available

## Security

* Api access can be controlled via IAM
* VPC SGs and ACL's can be also utilized
* ES provides fine grained IAM integration for full IAM Access control
* Resource-based policies
* • Identity-based policies
* • IP-based policies
* Request signing
* VPC
* Cognito
* Dashboards
  * Cognito
  * Getting inside a VPC from outside is hard… 
    • Nginx reverse proxy on EC2 forwarding to ES domain
    • SSH tunnel for port 5601 
    • VPC Direct Connect •
    * VPN

## Backups

* Supports Snapshots
* Can be automatic or manual
* Can be utilize to migrate ES domain between regions/accounts
* Cross -cluster replication
  • Replicate indices / mappings / metadata across domains
  • Ensures high availability in an outage
  • Replicate data geographically for better latency
  • “Follower” index pulls data from “leader” index
  • Requires fine -grained access control and node -to -node encryption
  • “Remote Reindex” allows copying indices from one cluster to another on demand

## Stability

• 3 dedicated master nodes is best 
    • Avoids “split brain” 
• Don’t run out of disk space 
   • Minimum storage requirement is roughly: Source Data * (1 + Number of Replicas) * 1.45
• Choosing the number of shards 
   • (source data + room to grow) * (1 + indexing overhead) / desired shard size
   • In rare cases you may need to limit the number of shards per node 
       • You usually run out of disk space first.
• Choosing instance types 
    • At least 3 nodes 
    • Mostly about storage requirements 
       • m6g.large.search, i3.4xlarge.search, i3.16xlarge.search

## Logstash

* Not included in ElasticSearch service domains
* ES Domains are a compatible backend for logstash implementations
* Kinesis replaces Beats & LogStash

## Performance

• Memory pressure in the JVM can result if:
  • You have unbalanced shard allocations across nodes
  • You have too many shards in a cluster
• Fewer shards can yield better performance if JVMMemoryPressure errors are encountered
  • Delete old or unused indices

# AWS Lake Formation

* Amazon S3 is not a valid import source in a Lake Formation blueprint

# AWS Security

## Traffic Mirroring

* Mirrors full packets
* Mirrored packets sent to ENI/NBL
* Similiar to tcpdump
* Can be targeted at
  * Source
  * Destination
  * Session (Source/Destination)
  * Filtering available like tcpdump


## HSM

• KMS => AWS manages the software for encryption
• CloudHSM => AWS provisions encryption hardware
• Dedicated Hardware (HSM = Hardware Security Module)
• You manage your own encryption keys entirely (not AWS)
• HSM device is tamper resistant, FIPS 140-2 Level 3 compliance
• CloudHSM clusters are spread across Multi AZ (HA) – must setup
• Supports both symmetric and asymmetric encryption (SSL/TLS keys)
• Must use the CloudHSM Client Software
• Redshift supports CloudHSM for database encryption and key management
• Good option to use with SSE-C encryption

## Kinesis

• Kinesis Data Streams
  • SSL endpoints using the HTTPS protocol to do encryption in flight
  • AWS KMS provides server-side encryption [Encryption at rest]
  • For client side-encryption, you must use your own encryption libraries
  • Supported Interface VPC Endpoints / Private Link – access privately
  • KCL – must get read / write access to DynamoDB table
• Kinesis Data Firehose:
  • Attach IAM roles so it can deliver to S3 / ES / Redshift / Splunk
  • Can encrypt the delivery stream with KMS [Server side encryption]
  • Supported Interface VPC Endpoints / Private Link – access privately
• Kinesis Data Analytics
  • Attach IAM role so it can read from Kinesis Data Streams and reference sources and write to an output destination (example Kinesis Data Firehose)

## SQS

• Encryption in flight using the HTTPS endpoint
• Server Side Encryption using KMS
• IAM policy must allow usage of SQS
• SQS queue access policy
• Client-side encryption must be implemented manually
• VPC Endpoint is provided through an Interface

## IOT

• AWS IoT policies:
  • Attached to X.509 certificates or Cognito Identities
  • Able to revoke any device at any time
  • IoT Policies are JSON documents
  • Can be attached to groups instead of individual Things.
• IAM Policies:
  • Attached to users, group or roles
  • Used for controlling IoT AWS APIs
• Attach roles to Rules Engine so they can perform their actions

## S3

• IAM policies 
• S3 bucket policies 
• Access Control Lists (ACLs) 
• Encryption in flight using HTTPS 
• Encryption at rest 
  • Server-side encryption: SSE-S3, SSE -KMS, SSE- C
  • Client -side encryption - such as Amazon S3 Encryption Client
• Versioning + MFA Delete 
• CORS for protecting websites 
• VPC Endpoint is provided through a Gateway 
• Glacier – vault lock policies to prevent deletes (WORM)

## DynamoDB

• Data is encrypted in transit using TLS (HTTPS) 
• DynamoDB tables are encrypted at rest 
• KMS encryption for base tables and secondary indexes 
• AWS owned key (default) 
• AWS managed key (aws/dynamodb) 
• AWS customer managed key (your own) 
• Access to tables / API / DAX using IAM 
• DynamoDB Streams are encrypted 
• VPC Endpoint is provided through a Gateway

## EMR

• Using Amazon EC2 key pair for SSH credentials
• Attach IAM roles to EC2 instances for:
   • proper S3 access
   • for EMRFS requests to S3
    • DynamoDB scans through Hive
• EC2 Security Groups
   • One for master node
   • Another one for cluster node (core node or task node)
• Encrypts data at-rest: EBS encryption, Open Source HDFS Encryption, LUKS + EMRFS for S3
• In-transit encryption: node to node communication, EMRFS, TLS
• Data is encrypted before uploading to S3
• Kerberos authentication (provide authentication from Active Directory)
• Apache Ranger: Centralized Authorization (RBAC – Role Based Access) – setup on external EC2