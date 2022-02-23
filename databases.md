__Databases__

- *Types of relational databases on AWS*
    1. SQL server
    2. Oracle
    3. MySQL
    4. PostgreSQL
    5. Amazon Aurora
    6. MariaDB

- Two main featues of RDS (OLTP - online transaction processing)
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
        - Must have automatic backups turned on in order to deploy a read replica
        - You can have up to 5 read replica copies of any database
        - You can have read replicas of read replicas (can appear issue with latency)
        - Each replica have its own DNS end point
        - You can have read replicas that have Multi-AZ
        - You can have a read replica in a second region
        - Read replicas can be promoted to be their own databases. However, this will brake replication and it will become a stand alone database

__Backups with RDS__
- Whenever you restore RDS (with bots methods), the new database will be created with a new DNS endpoint
- *Automated backups*
    - Allow to recover a database to any point in time within a retention period (Between 1 and 35 days).
    - Will take a full daily snapshot and will store transaction logs throughout the day
    - During recovery, AWS will first choose the most recent daily back up and then apply transaction logs relevant to that day (allows to do point in time recovery down to a second)
    - Are enabled by default
    - Backup data is stored in S3 (Get free storage space equal to the size of your database)
    - Backups are taken within a defined window. (Might experience latency and I/O may be suspended)
- *Database Snapshots*
    - Done manually and are stored even afer you delete the original RDS instance, unlike automated backups.

__Encryption__
- Encryption is done using KMS and supported on all database providers.
- Once encryption is enabled, data, automated backups, snapshots and read replicas are encrypted as well

- *RedShift (OLAP - online analytics processing)*
    - Used for Business Intelligence or Data Warehousing
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
        1. Memcached
        2. Redis

__DynamoDB__
    - Fast and flexiable NoSQL database service for all applications needing consistent, singe-digit milisecond latency at any scale.
    - Supports both document and key-value data
    - Stored on SSD storage (the reason in it fast)
    - Spread across 3 geographically distinct data centres
    - Eventual Consistent Reads (Default)
        - Consistency acroos all copies of data is usually reached within a second. (1 second delay)
        - Repeating a read after a short time should return the updated data (Best read performance)
    - Strongly consistent reads
        - Returns a result that reflects all writes that received a successful respnse priod to the read

__DynamoDB Accelerator (DAX)__
- Fully managed, highly available, in-memory cache
- 10x performance imiprovement
- Reduces request time from miliseconds to microseconds
- No need for developers to manage caching logic
- Compatible with DynamoDB API calls
- 