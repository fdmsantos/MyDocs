# AWS Certified Security Specialty

## Resources

[AWS Security](https://aws.amazon.com/security)

[White Paper: Introduction to AWS Security Processes](https://docs.aws.amazon.com/whitepapers/latest/aws-overview-security-processes/welcome.html?did=wp_card&trk=wp_card)

## Security 101

### Security Basics - Models

* CIA
    * Confidentiality - IAM, MFA
    * Integrity - Certificate Manager, IAM, Bucket Policies
    * Availability - Auto-Scaling, Multi-AZ

* AAA
    * Authentication - IAM
    * Authorization - Policies
    * Accounting - Cloudtrail

* Non-repudiation (Can't deny you did something)

### Shared Responsibility Model

* Infrastructure (EC2, EBS, VPC)

* Container (RDS, EMR, Elastic Beanstalk)

* Abstracted (S3, Glacier, DynamoDB, SQS, SES)

### Security IN AWS

* Visibility

    * AWS Config
    
* Auditability

    * AWS CloudTrail

* Controllability

    * AWS KMS - Multi-Tenant
    * AWS CloudHSM - Dedicated - FIPS 140-2 Compliance

* Agility

    * AWS Cloudformation
    * AWS Elastic Beanstalk
    
* Automation

    * AWS OpsWorks
    * AWS CodeDeploy
    
    
    
    
## IAM, S3 & Security Policies

### Resetting Root Users

* Create a new root user password and strong password policy
* Delete previous 2 factor authentication and re-create
* Check if the root user has an Access Key Id and Secret Access Key. If so delete these immediately
* Check other user accounts. Verify they are legitimate and if not, delete these

### IAM Policies

* IAM is global. Applies to all areas of AWS, not just S3
* Three different types os IAM Policies
    * AWS Managed Policies
    * Customer Managed Policies
    * Inline Policies
    

### S3 Bucket Policies

* S3 Bucket policies are attached only to S3 Buckets. S3 Bucket policies specify what actions are allowed or denied on the bucket. They can be broken down to a user level, So Alice can PUT but not DELETE and John can READ but not PUT.
* Bucket level only, S3 only
* Explicit Deny always Overrides an Allow
    * Use Cases
        * IAM Policies bump up against the size limit (up to 2kb for users, 5 kb for groups and 10kb for roles). S3 supports bucket policies of up 20kb
        

### S3 ACL's

* Are Legacy access control. AWS recommend IAm Policies and S3 Bucket Policies
* Use them if you need apply policies on the objects themselves. Bucket policies can be applied at bucket level, and S3 ACLS can be applied to individual files (Objects)

### Policy Conflicts - Visual Diagram

![Visial Diagram](../images/policy-conflicts-visual-diagram.jpg)

* With least-privilege, decisions ALWAYS default to DENY
* ALSO an explicit DENY ALWAYS trumps an ALLOW
* If no method specifies an ALLOW, then the request will be denied by default
* Only if no method specifies as DENY and one or more methods specify an ALLOW will the request be allowed

### Forcing Encryption using S3

* aws:SecureTransport

```json
# Deny This
"Condition": {
  "Bool": {
    "aws:SecureTransport": false
  }
}
```

### Cross Region Replication

* Requirements
    * Do not need to use a bucket policy with aws:SecureTransport to replicate objects using SSL. It is done by default
    * Versioning must be enabled (In two buckets)
    * Source and destination buckets must be in different AWS Regions
    * S3 must have permissions to replicate objects from that source bucket to the destination bucket on your behalf
    * It's possible to use CRR from one AWS account to another. The IAM role must have permissions ro replicate objects in the destination bucket.
    * If the object owner is different than source bucket owner, the object owner must grant the bucket owner the READ and READ_ACP permissions via the object ACL   
* In the replication configuration, can optionally direct Amazon S3 to change the ownership of object replica to the AWs account that owns the destination bucket
* What is Replicated
    * Any objects created after add a replication configuration
    * In addition to unencrypted objects, S3 replicates objects encrypted using S3 managed keys (SSE-S3) or AWS KMS managed keys (SSE-KMS)
    * Object metadata , ACL Updates, Tags
    * Replicates only objects in the source bucket for which the bucket owner has permissions to read objects and read access control lists (ACL)
    * If you just use a delete marker, then that delete marker is replicated
* What is NOT Replicated
    * Anything created before CRR is turned on
    * Objects created with Server-side encryption using customer-provided (SSE-C) encryption keys
    * Objects created with server-side encryption using AWS KMS - managed encryption (SSE-KMS) unless you explicitly enable this option
    * Objects in the source bucket for which the bucket owner does not have permissions. This can happen when the object owner is different from the bucket owner
    * Delete markers are replicated, deleted versions of files are NOT
    

### S3 with CloudFront

* Forcing S3 to Use cloudfront
    * It's necessary check "Restrict Bucket Access" option
    * It's necessary create a (or reuse) Origin Access Identity (something like a cloudfront user to access to bucket)
        * It's necessary update Bucket Policy to give permissions to Origin Access identity access to Bucket 
* CloudFront SSL Certificates
    * If you are happy for users to access your content using *.cloudfront.net domain name, then select the default CloudFront Certificate
    * If you want to use a domain name that you already own, you will need to use a custom SSL certificate
        * Custom SSL certificates must be stored in either ACM in the us-east-1 (North Virgina) or you can also store them in IAM using the IAM Cli.
        

### S3 Pre-Signed URLs

* You can access objects using pre-signed URL's
* Typically these are done via the SDK but can also be done using the CLI
* They exist for a certain length of time in seconds. Default is 1 Hour.
* You can change this using "--expires-in" followed by the number of seconds

### Security Token Service (STS)

Grants users limited and temporary access to AWS resources. Users can come from three sources:

* Federation (typically Active Directory)
    * Uses security assertion markup language (SAML)
    * Grants temporary access based off the users Active Directory credentials. Does not need to be a user in IAM
    * Single sign on allows users to log in to AWS console without assigning IAM credentials
* Federation with Mobile Apps
    * Use Facebook/Amazon/Google or other OpenID providers to log in
* Cross Account Access
    * Let's users from one AWS account access resources in another
    
Key Terms
* Federation: combining or joining a list of users in one domain (such as IAM) with a list of users in another domain (such as Active Directory, Facebook etc)
* Identity Broker: a service that allows you to take an identity from point A and join it (federate it) to point B
* Identity store: services like Active Directory, Facebook, Google etc
* Identities: a user of a service like Facebook etc

Use Case
1. Employee enters their username and password
2. The application calls an identity broker. The broker captures the username and password
3. The identity broker uses the organization's LDAP directory to validate the employee's identity
4. The identity broker calls the new GetFederationToken function using IAM credentials. The call must include an IAM policy and a duration (1 to 36 hours), along with a policy tha specifies the permissions to be granted to the temporary security credentials
5. The STS confirms that the policy of the IAM user making the call to GetFederationToken gives the permission to create new tokens and then returns four values to the application: An Access key, a secret access key, a token, and a duration (the token's lifetime)
6. The identity broker returns the temporary security credentials to the reporting application
7. The data storage application uses the temporary security credentials (including the token) to make requests to S3
8. S3 uses IAM to verify that the credentials allow the requested operation on the given S3 bucket and key
9. IAM provides S3 with the go-ahead to perform the requested operation

![Diagram](../images/screenshot.154.jpg)

### Web Identity Federation

* Federation allows users to authenticate with a web identity provider (Google, Facebook, Amazon)
* The User authenticates first with the Web ID Provider and receives an authentication token, which is exchanged for temporary AWS credentials
allowing them to assume an IAM role
* Cognito is an Identity Broker which handles interaction between your applications and the Web ID provider (You don't need to write you own code to do this)
    * Provides sign-up, sign-in, and guest user access
    * Syncs user data for a seamless experience across your devices
    * Cognito is the AWS recommended approach for Web ID federation particularly for mobile apps
    
### Cognito User Pools

* Cognito uses User Pools to manage user sign-up and sign-in directly or via web Identity Providers

* Cognito acts as an Identity broker, handling all interaction with Web Identity Providers

### Glacier Vault

* Glacier is low-cost storage for archiving and long-term backup
* Files are stored as Archives, Archives are stored in Vaults
* A Vault Lock Policy allows you to configure and enforce compliance controls for Glacier Vaults
* Configure WORM (write once read many) archives
* Create data retentions policys, 5 year retention
* Enforce regulartory and compliance controls
* You have 24 hours to validate the lock policy, once validated, Lock policies are immutable

### AWS Organizations

* Allows you to organize your accounts into groups / OUs for access control and centralized billing
* Attach policy based controls - Service Control Policies
* Centrally manage permissions for OUs - groups of accounts or individual accounts
* SCPs allow you to create a Permissions Boundary and restrict the permissions of child accounts including of the root user
* SCPs allow can deny, not allow

## Logging And Monitoring

### Cloud Trail

* Validating CloudTrail Log File Integrity
    * Was the log file modified, or deleted?
    * CloudTail log file integrity validation:
        * SHA-256 hashing
        * SHA-256 with RSA for digital signing
    * Log files are delivered with a 'digest' file
    * Digest file can be used to validate the integrity of the log file
* What is Logged
    * Metadata around API calls
    * The identity of the API caller
    * The time of the API call
    * The source IP address of the API caller
    * The request parameters
    * The response elements returned by the service
* CloudTrail Event Logs
    * Sent to an S3 bucket
    * You manage the retention in S3
    * Delivered every 5 (active) minutes with up 15 minute delay
    * Can be aggregated across regions
    * Can be aggregated across accounts
* Protect you CloudTrail logs, they contain everything that you are doing in your AWS account and may contain PII
* Allow your security people admin access to Cloudtrail, auditors read only access to CloudTrail using IAM
* Restrict Access to S3 using bucket policies, and use MFA delete on your objects
* Use lifecycle rules to move data to Glacier or to delete it
* Check the interity of your log files using digest files

### Cloudwatch

* Key Components
    * CloudWatch
    * CloudWatch logs
    * Cloudwatch Events
* Real Time
* Metrics
* Alarms
* Notifications
* Custom metrics
* CloudWatch Logs
    * Pushed from AWS services (including CloudTrail)
    * Pushed from your applications/systems
    * Metrics from log entry matches
    * Stored indefinitely (not user S3)
* Cloudwatch Events
    * Near real-time stream of system events
    * Events
        * AWS Resources state change
        * AWS CloudTrail (API Calls)
        * Custom Events (code)
        * Scheduled
    * Rules - match incoming events and route them to one or more targets
    * Targets - Lambda functions, SNS topics, SQS queues, Kinesis streams