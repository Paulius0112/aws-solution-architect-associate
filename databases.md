__Databases__

- *Types of relational databases on AWS*
    1. SQL server
    2. Oracle
    3. MySQL
    4. PostgreSQL
    5. Amazon Aurora
    6. MariaDB

- RAID 0 provides performance improvements compared with a single volume as data can be read and written to multiple disks simultaneously. 2 disks, each with a bandwidth of 4,000 IOPS will provide a combined bandwidth of 8,000 IOPS.

- Two main featues of *RDS* (OLTP - online transaction processing)
    1. Multi AZ - for disaster recovery
        - On fail, automatically changes DNS record to point to a secondary database instance
        - Allows to have an extra copy of your production database in another AZ
        - AWS handles the replication for you (changes will automatically be synchronized to the stand by database)
        - In the event of maintenance, failure or AZ failure, RDS will automatically failover to the standby so that database operations can resume quickly without administrative intervention
        - Available for SQL Server, Oracle, MySQL server, PostgreSQL, MariaDB
        - You can force a failover from one AZ to another by rebooting the RDS instance
        - You can only read/write to the primary, read only to the read replica
    2. Read replicas - for performance
        - Replicated data from primary to seondary database instances
        - On fail, you need manualy update connection string with new URL pointing to secondary database instance
        - Allows you to have a read-only copy of your production database. Achieved using Asynchronous replication from the primary RDS instance to the read replica
        - You use read replicas primarily for very read-heavy database workloads
        - Available for all databases
        - Are only used for scaling, not for DR.
        - Must have *automatic backups* turned on in order to deploy a read replica
        - You can have up to 5 read replica copies of any database
        - You can have read replicas of read replicas (can appear issue with latency)
        - Each replica have its own DNS end point
        - You can have read replicas that have Multi-AZ
        - You can have a read replica in a second region
        - Read replicas can be promoted to be their own databases. However, this will brake replication and it will become a stand alone database
- MariaDB, MySQL, Oracle, and PostgreSQL database instances: 20 GiB–64 TiB
- SQL Server Enterprise, Standard, Web, and Express Editions: 20 GiB–16 TiB
- To enable __multi-region replication of RDS__, we have to use __Read Replicas__. Multi-AZ is not the solution here.
- __RDS Read Replicas__ are __synced asynchronously__, so it can have __replication lag__.
- We can't use auto-scaling with __RDS__. To improve __performance__, we should look to __sharding__ instead. Starting from __June 20__, we __can use auto-scaling__ with RDS instances.
- We configure __RDS engine configurations__ using __parameter groups__.
- For RDS, __Enhanced Monitoring__ gathers its metrics from an __agent on the instance__.
- In case of a __failover__, Amazon RDS flips the canonical name record (__CNAME__) for your DB instance to point at the standby.
- The memory and processor __usage by each process__ in an RDS instance can not be monitored by Cloudwatch, we have to use __RDS Enhanced Monitoring__ for that. Because Cloudwatch monitors the hypervisor, not the individual instances.
- __IAM DB authentication__ can be used with __MySQL and PostgreSQL__. With this, you don't need to use a password when you connect to a DB instance. Instead, you use an __authentication token__.
- When you need to distribute load (such as for analytics), better to create **replica** and use that for analytics
- **Managed service** - and you do not have access to the underlying EC2 instance (no root access)

__Backups with RDS__
- Whenever you restore RDS (with bots methods), the new database will be created with a new DNS endpoint
- *Automated backups*
    - Allow to recover a database to any point in time within a retention period (Between 1 and 35 days).
    - Will take a full daily snapshot and will store transaction logs throughout the day
    - During recovery, AWS will first choose the most recent daily back up and then apply transaction logs relevant to that day (allows to do point in time recovery down to a second)
    - *Are enabled by default*
    - Backup data is stored in S3 (Get free storage space equal to the size of your database)
    - Backups are taken within a defined window. (Might experience latency and I/O may be suspended)
- *Database Snapshots*
    - Done manually and are stored even afer you delete the original RDS instance, unlike automated backups.
- If the database experiences high volume of change, by the time database is recreated, they can be out of sync
    - Therefore we need to use DMS to sync those databases

__RDS Encryption__
- **At rest Encryption** is done using KMS and supported on all database providers.
- Once **at rest encryption** is enabled, data, automated backups, snapshots and read replicas are encrypted as well
- Can only enabled while creating a database
- **SSL/TLS encryption in transit** - can be enabled by downloading AWS-provided root certificates and use them when connecting to DB. You can download one certificate for all regions or only region-specific

__Scalability of RDS__
- You can only scale RDS up (compute and storage)
- You cannot decrease the allocated storage for an RDS instance
- You can scale storage and change the storage type for all DB engines except MS SQL
    - Workaround is to create a new instance from snapshot with the new configuration
- Scaling compute will cause downtime
    - Scaling storage can happen while the RDS instance is running without outage however there may be performance degradation
- You can choose to have changes take effect immediately, however, the default is within the maintenance window
- All RDS types support a maximum DB size of 64 TiB except for Microsoft SQL server (16 TiB)

__HA approaches for databases__
- If possible, choose DynamoDB over RDS because of inherent fault tolerance
- If DynamoDB cant be used, choose Aurora because of redundancy and automatic recovery features
- If Aurora cant be used, choose Multi-AZ RDS
- Frequent RDS snapshots can protect aginst data corruption or failure, and they won't impact performance of Multi-AZ deployment

- *RedShift (OLAP - online analytics processing)*
    - Cost-effective to analyze all your data using standard SQL and existing BI tools
    - Clustered peta-byte scale data warehouse
    - SQL based for **analytics** application
        - Useful for running complex analytic queries against petabytes of *structured* data
        - Data is stored in columns for better performance
        - Automatically selects compression scheme
    - Provides *massively parallel processing* by distributing data and queries across all nodes
    - Can have both SSD and HDD
    - *Features parallel processing* and *columnar data stores* which are optimized for complex queries
    - Can store huge amounts of data but cannot ingest huge amounts of data in real time
    - Can be configured as follows:
        1. *Single Node (160Gb)*
        2. *Multi-Node*
            - *Leader Node* - manages client connections and receives queries
            - *Compute Node* - store data and perform queries and computations. Up to 128 Compute Nodes
    - **Amazon RedShift Spectrum** - feature that enables you to run queries against exabytes of unstructured data in Amazon S3, with no loading required

- *Availability and durability of redshift*
    - Uses replication and continuous backuops to enhance availability and durability
    - Automatically recovers from component or node failures
    - Only available in one AZ
        - You can run data warehouse clusters in multiple AZ's by loading data into two RedShift data warehouse clusters in separate AZs from the same set of Amazon S3 
    - Replicates your data within your cluster and backs up your data to s3
    - Always attempts to maintain at least three copies of your data (the original and replica on the compute nodes and a backup in S3)
    - Single-node clusters do not support data replication (in a failure scenario you would need to restore from a snapshot)
    - Scaling requires a period of unavailability of a few minutes (typically during the maintenance window)
    - By default, Amazon Redshift retains backups for 1 day. You can configure this to be as long as 35 days
    - Manual backups are not automatically deleted when you delete a cluster.

- *Security of RedShift*
    - You can load encrypted data from S3
    - Supports SSL encryption in transit
    - CloudTrail integration
    - Takes care of key management or you can manage your own KMS


- *High availability for RedShift:*
    - Currently, RedShift does not support Multi-AZ deployments.
    - The best HA option is to use multi-node cluster which supports data replication and node recovery.
    - A single node RedShift cluster does not support data replication and you’ll have to restore from a snapshot on S3 if a drive fails.

    - Used for Business Intelligence or Data Warehousing
    - Is great for *complex queries* and improves performance for repeated *queries* by caching
    - Fast and powerful, fully managed, petabyte-scale data warehouse service in the cloud
    - Can start small from just 0.25 dollars per hour with no commitments or upfront costs and scale to petabyte or more for 1000 dollars per terabyte per year, less than a tenth of most other dtaa warehousing solutions
    - *Pricing*
        - Compute node hours (total number of hours per compute node. You are not charged for leader node hours)
        - Backup
        - Data transfer (only within VPC, not outside)


- *ElastiCache*
    - Web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud.
    - Improves performance by retrieving information fast (basic cache working principals)
    - The problem with overloading database can be using elastiCache or Read Replicas.
    - Best for scenarios where the DB load is based on OLAP transactions
    - Push-button scalability for memory, reads and writes (without downtime)
    - Billed by node size and hours of use
    - Can be on-demand or reserved instances
    - Supports two open-source in-memory caching engines:
        1. Memcached (simple and easy to start)(Multi-threaded performance)
        2. Redis (advanced) (Multi-AZ, backups, Pub/Sub, Advanced data types, replication)
            - Can have authenitcation tokens enabled to require a password/token before allowing clients to execute commands
    | Memcached                                      | Redis                                        |
    |------------------------------------------------|----------------------------------------------|
    | Simple                                         | You need encryption                          |
    | You need elasticity (scale out and in)         | You need HIPAA compliance                    |
    | You need to run multiple CPU cores and threads | Support for clustering                       |
    | You need to chache objects                     | Complex data types, HA or Pub/Sub capability |

__DynamoDB__
- Fast and flexiable NoSQL database service for all applications needing consistent, singe-digit milisecond latency at any scale.
- Supports both document and key-value data
- Stored on SSD storage (the reason in it fast)
- Spread across 3 geographically distinct data centres
- *Eventual Consistent Reads (Default)*
    - Consistency across all copies of data is usually reached within a second. (1 second delay)
    - Repeating a read after a short time should return the updated data (Best read performance)
- *Strongly consistent reads*
    - Returns a result that reflects all writes that received a successful response priod to the read
- There will always be a charge for provisioning read and write capacity and the storage of data within DynamoDB, therefore these two answers are correct. There is no charge for the transfer of data into DynamoDB, providing you stay within a single region (if you cross regions, you will be charged at both ends of the transfer). There is no charge for local secondary indexes.
- AWS __DynamoDB__ is durable, ACID compliant, can go through multiple schema changes, and changes to the database does not result in any database downtime.
- __DynamoDB Global Tables__ can be used to deploy a multi region, multi AZ, fully managed database solution.
- We can create __secondary indexes__ for __DynamoDB__ tables. Always choose DynamoDB when possible.
- DynamoDB streams can be used to monitor changes made to a database, and they can trigger lambda functions.
- We can turn on __autoscaling for DynamoDB__.
- For __write heavy__ use cases in __DynamoDB__, use partition keys with large number of distinct values.
- __DynamoDB Accelerator, DAX__ is an __in-memory cache for DynamoDB__ that reduces response time from milliseconds to microseconds.
- **Best practises**
    - Keep item sizes small
    - Store more frequently and less frequently accessed data in separate tables
    - If possible compress larger attribute values
    - Store objects larger than 400kb in S# and use pointers from S3 object ID in DynamoDB 


__DynamoDB Accelerator (DAX)__
- Fully managed, highly available, in-memory cache
- 10x performance improvement
- Reduces request time from miliseconds to microseconds
- No need for developers to manage caching logic
- Compatible with DynamoDB API calls


__Aurora__
- On-demand, autoscaling configuration for MySQL and PostgreSQL compatible editions of AMazon Aurora. It automatically starts up, shuts down and scales capacity up or down depneding on your application needs
- 5 times better performance than MySQL and three times better than PostgreSQL at lower price and similar performance
- Starts with 10GB, scales in 10gb increments to 64TB (Storage autoscaling)
- Compute resources can scale up to 32vCPU and 244GB of memory
- 2 copies of your data is contained in each availability zone, with a minimum of 3 AZ. 6 copies of your data
- Aurora can handle the loss of up to two copies of data without affecting database write availability and up to three copies without affecting read availability
- Is self-healing. Data blocks and disks are continuously scanned for errors and repaired automatically
- It is simple and cost-effective solution for infrequent or unpredictable workloads
- *Replicas*
    - Aurora Replicas (currently 15)
    - MySQL Read Replicas (currently 5)
    - PostgresQL (currently 1)
- *Backups*
    - Automated backups are always enabled on Aurora and dont impact performance
    - Can take snapshots and does not impact performance
    - You can share snapshots with other AWS accounts
- *Aurora Global database*
    - Consists of one primary AWS region where your data is mastered, and up to five read-only, secondary AWS regions. 
    - Can issue writes only to main database
- *Aurora Serverless*
    - On-demand, auto-scaling configuration for amazon aurora. It automatically starts up, shuts down, and scales capacity up or down based on application needs. Ideal solution for infrequently-used applications

__Database Migration Service DMS__
- Migrate database into the AWS cloud, between on-premise instances, or between combinations of cloud and on-premises setups
- The source database remains fully operational during the migration, minimizing downtime to applications that rely on the database
- You can pre-create the target tables manually, or you can use *AWS Schema Conversion Tool* to create some or all of the target tables, indexes...
- Types of migrations:
    1. *Homogenous* for identical database engines (no need SCT)
    2. *Heterogeneous* for different databases. Need SCT
- The source can be on-premises, inside AWS itself or another cloud providers
- The source database remains fully operational during the migration, minimizing downtime to applications that rely on the database
- DMS is used for smaller, simpler conversions and supports MongoDB and DynamoDB
- SCT is used for larger, more complex datasets like data warehouses
- Supported migration paths:
    1. On-premises and EC2 databases to Amazon RDS or Aurora
    2. Homogeneous migrations such as Oracle to Oracle
    3. Heterogeneous migrations between different database platforms

__Amazon EMR__
- Big data platform for processing vast amounts of data using open-source tools such as Apache Spark, Hive, Flink.
- Can run petabyte-scale analysis at less than half the cost of traditional on-premises solutions and over three times faster than standard Apache Spark
- Central component is the cluster. Each node has a role within a cluster, refered as node type
    - Master node - tracks status of tasks and mointors the health of the cluster. Every cluster has a master node
    - Core node - has software components that runs tasks and stores data in Hadoop Distributed File system. Multi-node clusters have at least one core node
    - Task node - only runs tasks and odes not store data in HDFS. Is optional
- You can configure a cluster to periodically archive the log files stored on the master node to Amazon S3 at five minute intervals. Can only be set up when creating a cluster


- You don't need to specify a destination port number when you create DB security group rules. The port number defined for the DB instance is used as the destination port number for all rules defined for the DB security group.


__Notes on databases__
| Data Store | When to use |
|------------|-------------|
| **Database on EC2** | Ultimate control over database, preferred DB not available under RDS |
| **RDS** | Need OLTP relational database, your data is well formed and structured, existing apps requiring RDSMS |
| **DynamoDB** | Name/value pair data or unpredictable data structure, in-memory performance with persistence, high I/O needs, scale dynamically |
| **RedShift** | Massive amounts of data, primarily OLAP workloads |
| **ElastiCache** | Fast temporary storage for small amounts of data, highly volatile data |

__Anti-patterns__
| Requirement | More Suitable Service | 
|-------------|-----------------------|
| Lots of binary objects (BLOBs) | S3 |
| Automated scalability | DynamoDB |
| Data is not well structured or unpredictable | DynamoDB |
| Other database platforms not supported by RDS | EC2 |
| Complete control over the database | EC2 |