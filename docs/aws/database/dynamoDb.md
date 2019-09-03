# Monitoring

If an application’s read or write requests exceed the provisioned throughput for a
table, Amazon DynamoDB might throttle that request. When this happens, the
request fails with an HTTP 400 code (Bad Request), accompanied by a ProvisionedThroughputExceededException 

# High Availability

* Scalability
    * Unlimited amount of storage

* Elasticity
    * Increase additional IOPS for additional spikes in traffic. Drecrease that IOPS after spike
    * We can increase or decrease read and write throughput capacity on demand
    * As read requests increase, we can increase read throughput capacity
    * As read requests slow down, we can decrease capacity
