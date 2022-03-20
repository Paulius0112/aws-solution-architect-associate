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

- *RedShift (OLAP - online analytics processing)*
    - Used for Business Intelligence or Data Warehousing
    - Is great for *complex queries* and improves performance for repeated *queries* by caching
    - Fast and powerful, fully managed, petabyte-scale data warehouse service in the cloud
    - Can start small from just 0.25 dollars per hour with no commitments or upfront costs and scale to petabyte or more for 1000 dollars per terabyte per year, less than a tenth of most other dtaa warehousing solutions
    - Can be configured as follows:
        1. *Single Node (160Gb)*
        2. *Multi-Node*
            - *Leader Node* - manages client connections and receives queries
            - *Compute Node* - store data and perform queries and computations. Up to 128 Compute Nodes
    - *Uses advanced compression*
        - Columnar data stores can be compressed much more than row-based data stores because similar data is stored sequentially on disk.
        - Doesn't require indexes, so uses less space than traditional RDS
        - Uses multiple compression techniques (when uploading the data, AWS automatically samples your data and selects the most appropriate compression scheme)
    - *Massively Parallel Processing (MPP)*
        - Automatically distributes data and query load across all nodes
        - Makes easy to add nodes to your data warehouse and enables you to maintain fast query performance as your data warehouse grows
    - *Backups*
        - Enabled by default with 1 day retention period
        - Maximum retention period is 35 days
        - Always attempts to maintain at least three copies of your data (the original and replica on the compute nodes and a backup in S3)
        - Can also asynchronously replicate your snapshots to S3 in another region for disaster recovery
    - *Pricing*
        - Compute node hours (total number of hours per compute node. You are not charged for leader node hours)
        - Backup
        - Data transfer (only within VPC, not outside)
    - *Security considerations*
        - Encrypted in transit using SSL
        - Encrypted at rest using AES-256 encryption
        - By default RedShift takes care of key management
            - But you can manage your keys through HSM (Hardware securiy module) or KMS
    - *Availability*
        - Currently only available in 1 AZ
        - Can restore snapshots to new AZs in the event of an outage


- *ElastiCache*
    - Web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud.
    - Improves performance by retrieving information fast (basic cache working principals)
    - The problem with overloading database can be using elastiCache or Read Replicas.
    - Supports two open-source in-memory caching engines:
        1. Memcached (simple and easy to start)(Multi-threaded performance)
        2. Redis (advanced) (Multi-AZ, backups, Pub/Sub, Advanced data types, replication)

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

__Database Migration Service DMS__
- Migrate database into the AWS cloud, between on-premise instances, or between combinations of cloud and on-premises setups
- It is service on cloud that runs replication software.
- You can pre-create the target tables manually, or you can use *AWS Schema Conversion Tool* to create some or all of the target tables, indexes...
- Types of migrations:
    1. *Homogenous* for identical database engines (no need SCT)
    2. *Heterogeneous* for different databases. Need SCT
- The source can be on-premises, inside AWS itself or another cloud providers


__Caching capabilities__
- Technologies
    - Cloudfront
    - API Gateway
    - ElastiCache
    - DynamoDB Accelerator
- The more caching is placed infront, the less latency there will be

__Amazon EMR__
- Big data platform for processing vast amounts of data using open-source tools such as Apache Spark, Hive, Flink.
- Can run petabyte-scale analysis at less than half the cost of traditional on-premises solutions and over three times faster than standard Apache Spark
- Central component is the cluster. Each node has a role within a cluster, refered as node type
    - Master node - tracks status of tasks and mointors the health of the cluster. Every cluster has a master node
    - Core node - has software components that runs tasks and stores data in Hadoop Distributed File system. Multi-node clusters have at least one core node
    - Task node - only runs tasks and odes not store data in HDFS. Is optional
- You can configure a cluster to periodically archive the log files stored on the master node to Amazon S3 at five minute intervals. Can only be set up when creating a cluster


- You don't need to specify a destination port number when you create DB security group rules. The port number defined for the DB instance is used as the destination port number for all rules defined for the DB security group.