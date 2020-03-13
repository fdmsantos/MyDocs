# Associate Cloud Engineer

## Billing

* Export must be set up per billing account
* Resources should be placed into appropriate projects
* Resources should be tagged with labels
* Billing export is not real-time
    * Delay is Hours
   
## Cloud Storage

* Names are globally unique
* Location Type:
    * Region - Lowest latency within a single region
    * Multi-Region - Highest availability across largest area
    * Dual-Region - High availability and low latency across 2 regions
* Storage Class
    * Standard - Best for short-ter storage and frequently accessed data
    * Nearline - Best for backups and data accessed less than once a month
    * Coldline - Best for disaster recovery and data accessed les once a quarter
    * Archive  - Best for long-term digital preservation of data accessed less than once a year
* Access
    * Fine-Grained - Access per object (ACLS) in addition to bucket level permissions
    * Uniform - Permissions only using Bucket-Level permissions (IAM)
* Encryption
    * Google managed Key
    * Customer managed key
* Retention Policy
    * Minimum duration that this buckets objects must be protected from deletion or modification after they're uploaded.