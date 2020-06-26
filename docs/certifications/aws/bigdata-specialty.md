# AWS Certified Big Data Specialty

Recommended for study: Big Data Technology Fundamentals, Big Data Instructor led training class, Big Data AWS whitepappers and documentation

## Amazon Kinesis

### kinesis Streams

* Collect and process large streams of data in real time
* Create data-processing applications
    * Read data from stream
    * Process records
        * Send to a dashboard
        * Generate an alert
        * Dynamically change price
        * Dynamically change advertising strategy
* Scenarios
    * Fast log and data feed intake and processing
    * Real-time metrics and reporting
    * Real-time data analytics
    * Complex stream processing
* Benefits
    * Real-time aggregation of data
    * Loading the aggregate data into a data warehouse/map reduce cluster
    * Durability and Elasticity
    * Parallel application readers
* Methods to load/get data
    * Kinesis Producer Library (KPL)
    * Kinesis Client Library (KCL)
    * Kinesis Agent to collect and send
    * Kinesis Rest API
    
#### Core Concepts

* Shard
  * Uniquely identified group of data records in a stream
  * Single Shard Capacity
    * 1 MB/Sec data input
    * 2 MB/sec data output
    * 5 transactions/sec for reads
    * 1000 records/sec for writes
  * Can create multiple shards in a stream
    * Stream with 2 shards
        * 2 MB/Sec data input
        * 4 MB/sec data output
        * 10 transactions/sec for reads
        * 2000 records/sec for writes
    * Resharding - Dynamically add or remove shards as data throughput changes
        * Shard Split
            * Increase capacity and costs
        * Shard merge
            * Decrease capacity and costs
* Records
    * Unit of data stored in a stream
    * A record consists of
        * Partition Key
            * Group the data by shard
            * Tells you which shard the data belongs to
            * Partition Key is specified by the applications putting the data into a stream
        * Sequence Number
            * Unique identifiers for records inserted into a shard
            * Think of its as a unique key that identifies a data blob
            * Assigned when a producer calls PutRecord or PutRecords operations to add data to a stream
            * You can't use sequence numbers to logically separate data in terms of what shards they have come from. You can only do this using partition keys
        * Data Blob
            * Data blob is the data your data producer adds to a stream
            * The maximum size of data blob (the data payload after Base64-decoding) is 1MB
* Retention Period
    * 24 hours by default
    * Increase this to 7 days if required
    * Change retention period through CLI
* Data Producers
    * Amazon Kinesis Streams API
        * AWS SDK for Java
        * PutRecord
        * PutRecords
    * Amazon Kinesis Producer Library (KPL)
        * Configurable library to create producer applications that allow developers to achieve high write thoughput to a kinesis stream
        * Write to one of more Kinesis Streams with an auto-retry configurable mechanism
        * Collects records to write multiple records to multiple shards per request
        * Aggregate user records
        * kinesis Client Library integration (de-aggregate records)
        * Monitoring (CloudWatch)
        * Do Not use KPL if.....
            * Your producer application/use case cannot incur an additional processing delay
            * RecordMaxBufferedTime
            * Maximum amount of time a record spends being buffered
            * Larger values result in better performance, but can delay records
        * Batching
            * Aggregation
                * User records and streams records
                * Aggregation allows you to combine multiple users records into a single streams records, helping improve per shard throughput
                * Single Shard capacity is 1 MB/sec (write rate of 1000 records per second)
                * Example: 1000 records (500 bytes each) = 0.5 MB/Sec => Shard not efficient
            * Collection
                * Multiple Streams records are batched and sent in a single HTTP request with a call to the PutRecords API operation
                * Helps increase throughput due to reduced overhead of not making separate HTTP requests
             * Collection Vs Aggregation
                * Collection is working with groups of streams records and batching them to reduce HTTP requests
                * Aggregation allows you to combine multiple user records into a single streams records in order to efficiently use shards       
        * Error Handling
            * PutRecords operation sends multiple records to your stream per HTTP request (recommended approach for applications that require high throughput)
            * Single record failure does not stop the processing of subsquent records
            * Despite the failure of a single record, if other records in the PutRecords APi are successful, HTTP status code of 200 is still returned
            * For partial failures, the PutRecordsResult method in the KPL can be used detect individual record failures and retry the PUT based on the HTTP status code
    * Amazon Kinesis Agent
        * Web Servers, Log Servers, Database Servers
        * Download and install the agent
        * Monitor multiple directories and write to multiple streams
        * Pre-process data
            * Multi-line record to single line
            * Convert from delimiter to json format
* Data consumer
    * Kinesis Streams Applications using Kinesis Client Library (KCL)
        * KCL consume and process data
        * KCL handles complex tasks
        * Java, Node.js, .NET, Python and Ruby
        * Handles Checkpoint
            * Keeps track of records that have already been processed in a shard
            * If a worker fails, KCL will restart the processing of the shard at the last know processed record
        * Run your consumer application by running it on EC2 instance under an Auto Scaling group in order to replace failed instances or handle additional load
        * Automatically load balances record processing across many instances
        * Supports de-aggregation of records that were aggregated with the KPL
            * Uses unique DynamoDB table for each application to track application state
            * KCL uses the name of the streams application to create DynamoDB table, so use unique application names in KCL
            * Each row in the DynaoDB table represents a shard
            * Hash key for the DynamoDB table is the shard ID
            * DynamoDB table throughput
                * 10 read capacity units and 10 write capacity units
                * Provisioned throughput exceptions
                    * Your application does frequent checkpointing
                    * Your stream has too many shards
                    * Consider adding more provisioned throughput to the DynamoDB table
* For the exam
    * Any kind of scenario where you are streaming large amounts of data that needs to be processed quickly and you have a requirement to build a custom application to process and analyze streaming data
    * Shards
    * Record and it's components
    * Retention Period
    * Data Producers ( KPL, Kinesis Agent, Kinesis Streams API)
    * Data Consumers (KCL)
    * Additional throughput for DynamoDB application state table if you get provisioned throughput exception errors
    
#### Emitting Data to AWS Services

* Use Cases
    * S3 - Archiving data
    * DynamoDB - Metrics
    * Elasticsearch - Search and index
    * Redshift - Micro batch loading
    * EMR - Process and Analyze Data
    * Lambda - Automate emitting data
* Kinesis Connector Library
    * Java based
    * Used with the Kinesis Client Library
    * Connectors for
        * DynamoDB
        * Redshift
        * S3
        * Elasticsearch
    * Connector Library Pipeline
        1. Kinesis Stream
        2. iTransformer
            * Defines the transformation of records from the Amazon Kinesis stream in order to suit the user-defined data model
        3. iFilter
            * Excludes irrelevant records from the processing
        4. IBuffer
            * Buffers the set of records to processed by specifying size limit (# of records)& total byte count
        5. IEmitter
            * Makes client calls to other AWS services and persists the records stored in the buffer
            * S3Emitter Class
                * Writes buffer contents into a single file in S3
                * Requires the configuration of a S3 bucket and endpoint
        6. S3/DynamoDB/ElasticSearch/Redshift
* For the exam
    * Data can be emitted to S3, DynamoDBm Elasticsearch and Redshift from Kinesis Streams using the Kinesis connector Library
    * Lambda functions can automatically read records from a Kinesis stream, process them and send the records to S3, DynamoDB or Redshift

### Kinesis Analytics

### Kinesis Firehose