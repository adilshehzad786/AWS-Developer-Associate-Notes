# Storage

## Amazon Elastic Block Storage (EBS)

### EBS Types

- General Purpose SSD Volume (gps-gp3)
- Provisioned IOPS SSD (Io1/Io2)
- Throughput Optimized HDD(st1)
- Cold HDD(sc1)

![Untitled](https://user-images.githubusercontent.com/53600644/193906441-6bf60aaf-9479-4171-ab0c-b0eb7f7db2e8.png)


## S3

Even though each bucket is created in specific region, S3 names must be **unique** globally

### **S3's Object is consists of the following:**

- Key - full path: `<my_bucket>/my_folder/another_folder/my_file.txt`
- Value - content of the body
- Version ID
- Metadata
- Subresources (Access Control Lists, Torrent)

### **Data consistency work for S3:**

- Read after Write consistency for PUTS of new Objects
- Eventual Consistency for overwrite PUTS and DELETES

### **Features:**

- Tiered Storage Available
- Lifecycle Management
- Versioning
- Encryption
  - AES-256 (SSE-S3): use SSE with Amazon S3-Managed Key
  - AWS-KMS (SSE-KMS): use SSE with Amazon KMS-Managed Key
  - SSE-C: you manage your own encryption keys
  - Client Side Encryption
- MFA Delete
- signed URLs: URLs that are valid only for a limited time
- restrict access to S3 by **Access Control Lists** or **Bucket Policies**
- can create up to **100 buckets** per account by default
- object's size is from **0 bytes** up to **5TB**
- upload object > 100MB => **should** use multipart upload
- upload object > 5GB => **must** use multipart upload

### **S3 Storage Classes (tier)**

- S3 Standard (**99.99% availability** 99.999999999% durability )
- S3 - IA (Infrequently Accessed)
  - **99.9% availability** 99.999999999% durability
  - same low latency and high throughput performance of S3 Standard
  - minimum billable object storage size: 128KB
- S3 One Zone - IA
  - **99.5% availability** 99.999999999% durability
  - Same low latency and high throughput performance of S3 Standard
  - minimum billable object storage size: 128KB
- S3 - Intelligent Tiering
- S3 Glacier
  - Minutes to hours of restore time
  - **99.99% availability** 99.999999999% durability
  - minimum billable object storage size: 40KB
- S3 Glacier Deep Archive
  - 12 hours can acceptable restore time
  - **99.99% availability** 99.999999999% durability
  - minimum billable object storage size: 40KB

### **charged ways:**

- Storage
- Requests
- Storage Management Pricing (tier)
- Data Transfer Pricing
- Transfer Acceleration
- Cross Region Replication Pricing

**Tip: RRS (reduce redundancy storage) ⇒ S3 One Zone - IA**

### **S3 LifeCycle Management can:**

- Automates moving your objects between the different storage tiers
- can be used in conjunction with versioning
- can be applied to current versions and previous versions

### **Cross Region Replication**

- Versioning must be enabled on both the source and destination buckets
- Regions must be unique
- Files in an existing bucket are not replicated automatically (only when add a new file that new file was automatically replicated)
- All subsequent updated files will be replicated automatically
- Delete markers are not replicated
- Deleting individual versions or delete markers will not be replicated

### **S3 Transfer Acceleration**

Instead of uploading directly to S3 bucket, user can use a distinct URL to upload directly to an edge location which will then transfer that file to S3.
![Untitled 1](https://user-images.githubusercontent.com/53600644/193906506-78a6406f-b8dd-4f80-98b8-b4fdab933bb5.png)


### Access Point

- managing data access at scale for applications using shared data sets on S3
- bucket can have hundreds of access points
- can create an access point for your S3 bucket that grants access for groups of users or applications for your data lake. An Access Point could support a single user or application, or groups of users or applications, allowing separate management of each access point

### Exams Tip:

- protected against inadvertent or intentional deletion ⇒ versioning (not cross-region replication)

## Encryption in rest

### SSE-S3

- encryption using keys handled & managed by S3
- object is encrypted server side
- AES-256 encryption type
- set header `"x-amz-server-side-encryption": "AES256"`

![Untitled 2](https://user-images.githubusercontent.com/53600644/193906628-378bca22-f86f-48df-8567-4a31ba7b8a5f.png)


### SSE-KMS

- encryption using keys handled & managed by KMS
- KMS Advantages: user control + audit trail
- object is encrypted server side
- set header `"x-amz-server-side-encryption": "aws:kms"`
![Untitled 3](https://user-images.githubusercontent.com/53600644/193906690-2f4f0de6-d2d6-467e-8167-c79f2ac73686.png)



### SSE-C

- encryption using keys handled & managed by client
- object is encrypted server side (S3 use key to encrypt then through key - don't store any things)
- HTTPS must be used
- encryption key must provided in HTTP headers, for every HTTP request made
- set header
  - `x-amz-server-side​-encryption​-customer-algorithm`
  - `x-amz-server-side-encryption-customer-key`
  - `x-amz-server-side-encryption-customer-key-MD5`

![Untitled 4](https://user-images.githubusercontent.com/53600644/193906725-805e0a64-da7c-40a6-aec0-96f64fd2e060.png)


### Client side encryption

- client encrypt data before sending to S3
- client decrypt data after retrieving from S3
- client fully manages the keys and encryption cycle
![Untitled 5](https://user-images.githubusercontent.com/53600644/193906741-4edc242e-fc4f-4566-a0e0-ec8caf577617.png)



## Encryption in transit (in fly)

use SSL/TLS (https) to access to S3 endpoint

## Bucket policies

- json based:
  - resources: buckets and objects
  - Actions: set of API to Allow or Deny
  - Effect: Allow/ Deny
  - Principal: The account or user to apply the policy to
- use policy to:
  - Grant public access to the bucket
  - force objects to be encrypted at upload

## S3 CORS

- if you host a static website on S3, and you . request data from another S3 bucket, you need enable CORS
- CORS allow you to limit the number of websites that can request your file in S3
- read more: [CORS - Cross Same Origin Policy](https://www.notion.so/CORS-Cross-Same-Origin-Policy-98390b3dd7554abcbad0a53485ee75d9)

## S3 Performance

- it was recommend to have random characters in front of your key name to optimize performance
  - <my_bucket>/**5r4d**_my_folder/my_file1.txt
  - <my_bucket>/**a91e**_my_folder/my_file2.txt
- is was recommend to never to use dates to prefix keys:
  - ~~<my_bucket>/2018_09_09_my_folder/my_file1.txt~~
  - ~~<my_bucket>/2018_09_10_my_folder/my_file2.txt~~
- from 2018/07, up to 3500 RPS for PUT, and 5500 RPS for GET ⇒ don't need to add random characters
- use CloudFront for read
- S3 Transfer Acceleration for write
- If using SSE-KMS encryption, you may be limited to your AWS limits for KMS usage ⇒ increase the KMS limits

## S3 + Glacier Select

- only retrieve some parts of data
- `select * from s3object s where s.\"Country (Name)\" like '%United States%'`
- save cost up to 80% and increase performance up to 400%
- work with files in CSV, JSON, Parquet format
- can be compressed with GZIP or BZIP2
- no sub queries or Join queries

## Kinesis

### data is automatically replicated to 3 AZ

- **Kinesis Streams**: low latency streaming ingest at scale
- **Kinesis Analytics**: perform real-time analytics on streams using SQL
- **Kinesis Firehose:** load streams into S3, Redshift, ElasticSearch

![Untitled 6](https://user-images.githubusercontent.com/53600644/193906835-6bd4038b-9272-4bca-9d3a-8704b0e5712d.png)


## Kinesis Streams

- streams are divided in ordered Shards / Partitions
- retentions: 1 (default) - 7 days
- ability to reprocess/replay data
- multiple apps can consume the same stream
- real-time processing with scale of throughput
- one data is inserted in Kinesis, it can't be deleted (immutability)

### Shard

![Untitled 7](https://user-images.githubusercontent.com/53600644/193906868-88e486f3-cddd-4770-9982-2263582e9b9b.png)


- one stream is made of many different shards
- 1MB/s or 1000 messages/s at write PER SHARD
- 2MB/s at read PER SHARD
- billing is per shard provisioned
- batching available or per message calls
- number of shards can change over time (reshard/merge)
- records are ordered **per shard**

### Put records

- data + partition key (message key) ⇒ PutRecord
- partition key to determine shard id
- messages sent get a "sequence number"
- choose a partition key that is highly distributed
  - user_id if many users
  - **Not** country_id if 90% of the users are in one country
  - **Not** date
- use Batching with PutRecords to reduce costs and increase throughput
- **ProvisionedThroughputExceeded** if go over the limits

### Consumers

- can use normal consumer
  
- can use Kinesis Client Library
  
  - read record from a Kinesis Streams with distributed apps sharing the read workload (run on EC2, Elastic Beanstalk, on-premise)
    
  - each shard is read by only 1 KCL instance
    
  - means 4 shards = max 4 KCL instances, means 6 shards = max 6 KCL instances
    
  - progress is check pointed into DynamoDB (need IAM access)
    
![Untitled 8](https://user-images.githubusercontent.com/53600644/193906878-712da3de-39c6-4de5-ae7c-a19ec5389b95.png)



### Exceptions

- ProvisionedThroughputExceeded: over the limits of any shard
  - make sure don't have a hot shard
  - solutions:
    - retries with backoff
    - increase shards
    - ensure good partition key

## Kinesis Analytics

- perform real-time analytics on Kinesis Streams using SQL
- auto scaling
- don't need provision
- can create streams out of the real-time queries (windowed average)
- pay for actual consumption rate

## Kinesis Firehose

- near real time (60s latency)
- load data into Redshift / S3 / ElasticSearch / Splunk
- auto scaling
- don't need provision
- pay for the amount of data going through Firehose

## Security

- control access / authorization using IAM policies
- Encrypt in flight using HTTPS
- Encrypt at rest using KMS
- possible to encrypt / decrypt data client side
- VPC Endpoints available for Kinesis

## SQS vs SNS vs Kinesis

- consumer "pull data"
  
- data is deleted after being consumed
  
- can have as many workers (consumers) as we want
  
- no need to provision throughput
  
- no ordering guarantee (except FIFO queues)
  
- individual message delay capability
  
- push data to many subscribers
  
- up to 10,000,000 subscribers
  
- data is not persisted (lost if not delivered)
  
- Pub/Sub
  
- Up to 100,000 topics
  
- No need to provision throughput
  
- Integrates with SQS for fan-out architecture pattern
  
- consumers " pull data"
  
- as many consumers as we want (1 shard - 1 consumer)
  
- possible to replay data
  
- meant for real-time big data, analytics and ETL (IOT)
  
- ordering at the shard level
  
- data expires after X days
  
- must provision throughput
