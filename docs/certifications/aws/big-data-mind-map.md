# AWS Big Data

## Streaming

### Kinesis Data Streams

* Data Retention: 24 Hours <> 365 Days
* At Least Once Delivery
* Replay Data
* Immutability
* Shard
  * Daa ordered by shard
  * 1000 records per second
  * Payload Per Record: 1Mb
  * Record Key Per Shard -> Same Key -> Same Shard
  * Producers
    * 1 MB/Sec Input or 1000 messages at write
    * KPL
      * Support Batching
      * Comprehension implemented by user
      * Records Must be decoded by KCL or special helpers
      * Don't use with applications can't tolerate delays. Use SDK Instead
    * Agent
      * Only Linux
      * Monitors logs files and sent to data Streams
    * SDK
      * Support Batching via Put Records
      * ProvisionedThroughtputExceed -> Retries with backoff, increase shards, ensure partition key is good
      * No Delays
  * Consumers
    * 2 MB/Sec Output across all consumers
    * 5 API calls per second across all consumers
    * SDK
      * Get Records returns up 10Mb of data (then throttle for 5 seconds) or up 10000 records
      * Maximum of 5 GetRecords APi calls per shard per second = 200 ms latency
    * KCL
      * Read Records from Kinesis produced with the KPL (de-aggregation)
      * Share multiple shards with multiple consumers in one group, shard discovery
      * Checkpoint Feature (Uses DynamoDB)
        * Make sure your provision enough WCU/RCU. ExpiredIteratorException => increase WCU
    * 3rd Party libraries
      * Spark, Log4j Appenders, flume, Kafka connect
    * Kinesis Firehose
    * Lambda
      * Lambda consumer has a library to de-aggregate record from the KPL
      * Can be used to trigger notifications / send emails in real time
      * Configurable batch size. Too Large Can cause Timeouts
      * Write to S3/DynamoDB/Redshift/ElasticSearch/Anywhere you want
      * Lambda will retry the batch until it succeeds or the data expires
        * this can broke your shard. Use more shards to ensure processing isn’t totally held up by errors
    * Consumer Enhanced Fan-Out
      * Low latency requirements (70ms)
      * 2 MB/Sec Output per enhanced consumer
      * No API calls needed (push model / subscribe)
  * Scaling
    * Add Shard (Shard Split) - To Divide Hot shard
    * Merge Shards - To group two shards with low traffic
    * Auto Scaling
      * Not Native Feature
      * Can implement Auto scaling with Lambda (trigger lambda based CW Alarm). UpdateShardCount Api call
    * After reshard, read entirely from the parent until don't have new records
      * KCL has this logic already built-in
    * Old Shards are closed and deleted based on data expiration
  * Duplicates
    * Producers: Embed unique record ID in the data to de-duplicate on the consumer side
    * Consumers:
      * Make your consumer application idempotent
      * If the final destination can handle duplicates, it's recommended to do it there
* Security
  * IAM
  * HTTPS
  * KMS
  * Client Side encryption must be manually implemented (hard)

### Kinesis Firehose

* Record can be large as 1000Kb
* Buffer Interval (60 -> 900 seconds)
* Can transform Data Via Lambda
  * Transformation Failures or Delivery Failures can be archived in other S3 Buckets
  * Support backup bucket to original records
* Destinations
  * S3
    * Can convert from JSON to Parquet/ORC
    * Can Apply comprehession: GZIP, ZIP and Snappy
    * Buffer Size (1Mb <-> 128 Mb)
    * Data Organized -> Uses the UTC time prefix in the format : YYYY/MM/DD/HH . File: DeliveryStreamName-DeliveryStreamVersion-YYYY-MM-DD-HH-MM-SS-RandomString
  * Redshift
    * First goes to S3 bucekts and is copy to redshift
    * Can run lambdas to transform records before storing in S3 or before storing in redshift
    * Supports GZIP Comprehension
  * Elasticsearch
    * Buffer Size (1 Mb <-> 100 Mb)
  * Splunk Instance, New Relic, DataDog, MongoDB
  * Http Endpoint
* Sources
  * Applications, clients, SDK, KPL, Agent
  * Kinesis Data Streams
  * cloudwatch, IOT
  
### Kinesis Data Analytics

* Support SQL and Apache Flink Runtimes
* Supports Reference Data In S3
* Error Handling
* Pre-Processing with Lambda
* RANDOM_CUT_FOREST => SQL function used for anomaly detection on numeric columns in a stream
* Inputs
  * Data Streams
  * Firehose
  * S3
* Outputs
  * Data Streams
  * Firehose
  * Lambda
    * Opens up access to other services & destinations
  * Output format
    * CSV
    * JSON

### Kinesis Video Streams

* Stored S3
* Supports SNS
* Producers
  * Uses Kinesis Video Stream Libraries
* Consumers
  * EC2
  * AWS Rekognition
  * AWS Services
  * Third Party Services

### SQS

* Low Latency (10 ms)
* Retention: 1 Minute to 14 Days
* At Least Once Delivery
* No Order (Best Effort)
* 256 Kb Per message
  * SQS Extended Client
    * To send large Messages. Large Message is sent to S3, and one small metadata message to Queue
* Can Delay Messages
* Batch Request Maximum 10 Messages
* Fifo Queues
  * Order Messages
  * Exactly Once delivery
* Security
  * Https
  * KMS with CMK
  * IAM
  * SQS Access Policy

### IOT

### MSK

* Data Retention -> can be unlimited
* Data Stored in EBs
* Possibility to send Large Messages (Ex 10Mb)
* Security
  * Encryption https / at rest EBS Volumes using KMS
  * Authentication -> Mutal TLs, and Kakga ACLS. Also IAM

## Processing

### EMR

* Cluster
  * Primary Nodes
    * Single or Multi
  * Core Nodes
    * Data daemon runs here
  * Task Nodes
    * Optional helpers to add power to perform parallel computations
    * No data daemon
* Scaling
  * Managed Scaling
    * Instance Fleets
    * Instance Groups
    * Only Yarn Applications: Presto not supported
    * 5 <-> 10 seconds
  * Custom Scaling
    * Instance Groups
    * 5 Minutes
    * Based CW Metrics
    * Custom Applications
* Storage Options
  * Local File System (Instance Store)
  * EBS (Local File System)
  * HDFS (Hadoop Distributed File System)
    * Ephemeral
    * Files Stored as blocks (128MB default size)
  * EMRFS
    * Works well for jobs that read a dataset once per run
    * Persistent Storage
    * Best used for persistent store and S3 features that are needed, like server side encryption and consistency
* S3DistCp
  * Tool for copying large amounts of data
  * Suitable for parallel copying of large numbers of objects
  * Across buckets, across accounts
  * Dont support concat Parquet Files
* Replication Factor
  * Changed in rdfs-site.xml
  * 10+ Instances -> replication factor is 3
  * 4-9 Instances -> replication factor is 2
  * 1-3 Instances -> replication factor is 1
  * Calculate HDFS Capacity
    * Number nodes x Storage Capacity / replication factor
* Resize Cluster
  * Can't have less core nodes than the replication factor
    * Change replication factor in hdfs-site.xml and restart named node daemon
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
* Security
  * Two security groups are setup. One for primary node, and one for the secondary nodes (core and task nodes)
  * Iam Policies
  * Kerberos
    * Secure user authentication
  * SSH
    * Tunneling for web interfaces
    * Can use Kerberos or EC2 key pairs
  * Block Public Access
    * Easy way to prevent public access to data stored on your EMR cluster
    * Can set at the account level before creating the cluster.
* Encryption
  * KMS Encryption
  * LUKS Encryption
  * SSE-KMS
    * Data in S3
  * CSE-KMS
    * EMR First encrypts the data with a CMK, then sends to S3 for storage
    * Data in S3
  * SSE-S3
    * Data in S3
* Hadoop Ecosystem
  * Spark
    * Integrates with Kinesis
    * Integrates with Redshift
  * Hive
    * Load Table partitions /alter table / Write tables from/to S3
    * Load Scripts from S3
    * DynamoDB as external Table
      * Performs Joins in DynamoDb Table
  * Pig
    * Higher level to Map Reduce
    * Pig introduces Pig Latin, a scripting language that lets you use SQL like syntax to define your map and reduce steps.
  * Hbase
    * Non-relational, petabyte -scale database (Based on Google Big Table on top of HDFS)
  * Presto
    * It can connect to many different “big data” databases and data stores at once, and query across them
  * Zeppelin
    * If you’re familiar with iPython notebooks – it’s like that
  * EMR Notebook
    * Similar concept to Zeppelin, with more AWS integration
    * Hosted inside a VPC
    * Accessed only via AWS console
  * Hue
    * Hadoop User Experience - Graphical front-end for applications on your EMR cluster
    * IAM integration: Hue Super - users inherit IAM roles
  * Splunk
    * Splunk / Hunk “makes machine data accessible, usable, and valuable to everyone”
    * Operational tool – can be used to visualize EMR and S3 data using your EMR Hadoop cluster.
    * Reserved instances on 64-bit OS recommended
  * Flume
    * Another way to stream data into your cluster
  * MXNext
    * Like Tensorflow, a library for building and accelerating neural networks
  * Ganglia (monitoring) => Operational Dashboard
  * Mahout (machine learning)
  * Accumulo (another NoSQL database)
  * Sqoop (relational database connector)
  * HCatalog (table and storage management for Hive metastore)
  * Kinesis Connector (directly access Kinesis streams in your scripts)
  * Tachyon (accelerator for Spark)
  * Derby (open-source relational DB in Java)
  * Ranger (data security manager for Hadoop)

### Glue
  
## Storage

### Redshift

* Cluster
  * Leader Node
    * Schema Management
    * Warehouse Metadata
    * Query Planning
  * Worker Node
    * Query Execution
    * Slice Management
* Node Type
  * Dense Compute
  * Dense Storage
  * RA3
* Dblink allows you to offload queries in Redshift to another database entirely.
  * Connect Redshift to PostgreSQL (possibly in RDS)
  * Good way to copy and sync data between PostgreSQL and Redshift
* Table Design
  * Comprehension
    * ANALYZE COMPRESSION Give you the smallest, not the best performance
    * Compress Individual tables, change requires table rebuild
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
    * LZO
      * Char and VarChar
  * Sort Keys
    * Single Column
    * Compound
      * Default
      * By Operations (SORT, Order, Partition by, GROUP BY )
      * Multi-column sorting, Hierarchical Data
    * Interleaved
      * Increase Table Maintenance
      * Best for where predicates
    * Best Pratices
      * Use on Columns that are frequently filtered on placing the lowers cardinality columns first
      * On most fact tables, the first sort key column should be a temporal column
      * Columns added to a sort key after high-cardinality column are not effective
      * Sort Keys are less beneficial on small tables
      * Define four or less sort Keys columns -- more will result in marginal gains and increased ingestion overhead
  * Distribution Styles
    * Even
      * Blocks are distributed evenly between cluster slices (Default)
      * if neither KEY or ALL Apply
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
      * Redshift figures it out based on size of data
* Spectrum
  * Supports SELECT and INSERT operations
  * UPDATE and DELETE are not supported -> These operations need to be done outside redshift spectrum
* Resize
  * Classic Resize
    * Hours to Days
    * Change Node Type
    * Read Only Mode
    * Use Cases
      * Change cluster instance type (SSD to HDD)
      * If elastic resize is not an option because of sizing limits
  * Elastic Resize
    * Minutes
      * Inflight queries/connections are put on hold
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
* Scaling
  * Vertical and horizontal scaling on demand
  * During scaling:
    * A new cluster is created while your old one remains available for reads
    * CNAME is flipped to new cluster (a few minutes of downtime)
    * Data moved in parallel to new compute nodes
* Vacuum
  * VACUUM Full
    * Reclaim Disk Space
    * Sort Table
    * Reindex Table
      * Interleaved tables require explicit VACUUM REINDEX
    * Use Cases
      * Useful to maintenance period
      * Lots disk to reclaim
      * Lots unsorted rows
  * VACUUM SORT ONLY
  * VACUUM DELETE ONLY
  * VACUUM REINDEX table_name
    * Performs VACUUM FULL on
    * Interleaved Tables
  * BOOST
    * Ignores cluster activity and contends for resources like other processes
    * Use in maintenance Window
  * Automatic
    * Automatic Vacuum Delete
    * Automatic Table sort
    * Automatic Analyze
* Deep Copy
  * Create New Table, move data and delete the resources marked for deletion. Truncate Source and Rename target to source
  * Can be less time than full vacuum
  * Methods
    * Use Original Table DDL
    * Create Table Like
    * Temp Table, Truncate
* Analyze
  * Use to collects table statistics for optimal query planning
  * Best Pratices
    * ANALYZE can be run periodically after ingestion on just the colummns that where predicates are filtered on
* Copy Command
  * From S3, EMR, DynamoDB, remote hosts
    * S3 Requires manifest file
    * Can decrypt data as it is loaded from S3
      * Hardware-accelerated SSL used to keep it fast
    * Automatic compression option
  * Performance
    * Single Copy to Load multiple files is better than concorrent copy commands
      * Number of input files should be a multiple of the number of slices
      * Splitting the single file into 16 (number of slices) input files, all slices are working to maximize ingestion performance
    * Delimited files are recommended -- 1MB to 1GB after gzip compression
      * PICK simple delimiter, PICK simple NULL Character (\N)
    * Use double quotes and escape character for varchars
    * UTF8 varchar columns take four bytes per char
    * Gzip, lzop, and bzip2 compression supported to speed it up further
    * Special case: narrow tables (lots of rows, few columns)
      * Load with a single COPY transaction if possible
      * Otherwise hidden metadata columns consume too much space
* Backup and Restore
  * Snapshots
  * Loading Data From S3
  * Unload Data To S3
* Deduplication/Upsert
  * Create transaction with this process
    * Load CSV data into a staging table
    * Delete duplicate data from the production table
    * Insert (or append) data from the staging into the production table
* ETL
  * Wrap workflow/statements in an explicit transaction
  * Consider using DROP TABLe or TRUNCATE instead of delete
  * Staging Tables:
    * If possible use DISTSTYLE KEY on both the staging table and production table to speed up the INSERT INTO Select statement
    * With COPY turn of automatic compression - COMPUDATE OFF
    * Copy compression settings from the production table (using like keyword) or manually apply compression to CREATE TABLE DDL (from ANALYZE COMPRESSION output)
    * For copying a large number of rows (> hundreds of millions) consider using ALTER TABLE APPEND instead of INSERT INTO SELECT
* Security
  * Using a Hardware Security Module (HSM)
    * Must use a client and server certificate to configure a trusted connection between Redshift and the HSM
    * If migrating an unencrypted cluster to an HSM-encrypted cluster, you must create the new encrypted cluster and then move data to it.
  * Defining access privileges for user or group
    * Use the GRANT or REVOKE commands in SQL
