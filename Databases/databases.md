# Databases

| Service | Type | Relation |
| --- | --- | --- |
| Amazon RDS | Relational Database | A Managed Database for MySQL, PostgreSQL , Oracle , SQL Server and MariaDB |
| Amazon DynamoDB | Non-Relational Database | A Serverless managed NoSQL database that delivers consistent single-digit millisecond latency at any scale. |
| Amazon Redshift | Data Warehouse | A Fast Full managed petabyte-scale data warehouse at one-tenth the cost of traditional solution |
| Amazon Elastic Cache | In Memory Data store | To deploy , operate and scale an in memory data store based on Memcached or Redis |
| Amazon Neptune | Graph Database | A Fast , Reliable full managed graph database to store and manage highly connected datasets. |
| Amazon Document DB (MongoDB) | Non Relational | A fast , scalable , highly available and fully managed document database service that supported MongoDB workloads |
| Amazon timestream | Time Series Database | A Fast Scalable, fully managed time service database for IoT and operational applications. |
| Amazon Quantum | Ledger Database | A fully managed ledger database that provides transparent, immutable, and cryptographically verifiable transactions. |
| AWS DMS | Data Migration | Help migrate your databases to AWS easily and inexpensively with minimal downtime. |

| Applications | Service |
| --- | --- |
| Transactional Applications such as ERP , CRM and ecommerce to log transactions and store structured data. | Amazon RDS |
| Internet scale application such as hospitality , dating and ride sharing to server content and store structure and unstructured data | DynamoDB or DocumentDB |
| Analytics Applications for operational reporting and querying terabyte to exabyte scale data | Redshift |
| Real-time application use cases that require sub millisecond latency such as gaming lead boards , chat messaging , streaming and IoT | AWS Elastic Cache |
| Applications with use cases that require navigation of highly connected data such as social news feeds recommendations and fraud detection. | Neptune |
| Applications that collect data at millions of inserts per second in a time series fashion such as clickstream data | Timestream |
| Application that require an accurate history of their application data | Amazon QLDB |

## RedShift

### Leader Node

A Leader node acts as SQL endpoint and receives queries from client application parses the queries and develops query execution plans.

### Compute Nodes

Computer node execute the query execution plan and transmit data among themselves to serve these queries.

### Node Slices

A Computer node is partitioned into slices. Each slice is allocated a portion of the node memory and disk space.

## Amazon ElastiCache

Amazon ElasticCache is a web service that make it easy to deploy , operate and scale an in-memory cache in the AWS Cloud. The service improves the performance of web applications by allowing you to retrieve information from fast, managed , in-memory cache instead of relying entirely on slower disk -based database.

### Redis

Redist is an opensource, key-value store that supports more advanced data structures such as sorted sets , hashes and lists . Unlike Memcached , Redis has disk presistence built in , meaning that you can use it for long-lived data.

### MemCached

MemCached is a widely adopted in-memory key store . it is histoically that gold standard of web caching. Elastic Cache is protocol compliant with memcached and it is designed to work with popular tol that you can use today with existing memcached enviornments.