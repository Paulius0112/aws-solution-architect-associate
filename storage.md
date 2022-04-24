## EFS
- Fully managed service for hosting **Network File System** and accessed using NFS protocol
- Storage capacity is elastic, **growing and shrinking automatically** as you add and remove files
- Instances **can share single EFS**, but not EBS
- Can configure mount-points in one, or many AZs
- You can mount an AWS EFS filesystem from on-premises systems only if you are using **Direct Connect** or **VPN connection**
- Integrates **life cycle policies**
    - Lifecycle management moves files that have not been accessed for a period of times to the **EFS infrequently access storage** class
- There are two performance modes:
    1. **General Purpose** - performance mode is appropriate for most file systems
    2. **Max I/O** - performance mode is optimized for applications where tens, hundreds, or thousands of EC@ instances are accessing the file system
- You pay only for what you use
- Can **support thousands of concurrent NFS connections** and can scale up to the petabytes
- Data is stored across **multiple AZ's** within a region
- Read After Write consistency
- EC2 running windows cannot connect to EFS, only linux and unix based. If it is windows server, it should use FSx Windows
- EFS supports cross availability zone mounting, but it is not recommended. The recommended approach is __creating a mount point in each availability zone__.
- You can mount an EFS file system in only one VPC at a time. If you want to access it or mount it from another VPC, you have to create a __VPC peering connection__. You should note that all of these must be within the same region.
- __Encryption__
    1. Encryption at rest must be specified at the creation of file system. If you want to modify it later on, create a new EFS file system with encryption enabled and copy the data over.
    2. Encryption at transit is supported by EFS // NFS, and must be enabled from the client side. It simply uses SSL to encrypt the connection.
- __Performance mode__
    1. General purpose must be used for most purposes, it has low latency, so ideal for web applications.
    2. Max IO is ideal for big data and parallel connection and processing from a large number of hosts. It has higher latency but large throughput.
- __Throughput mode__
    1. Bursting is ideal for arbitrary large amount of data, because it scales properly.
    2. But for cases with high throughput to storage ratio, such as common in web applications, provisioned mode is better.
- You can control who administer your file system using IAM
- You can control access to files and directories with POSIX-compliant user and group-level permissions. Allows you to restrict access from hosts by user and group
- EFS security group act as a firewall, and the rules you add define the traffic flow


## EBS
- Elastic block storage. They are **network attached storage** and can be attached to EC2
- Provides **persistent storage**, while **instance store** does not
- Data is replicated across multiple servers in an AZ
- **Termination** protection is turned off by default and must be manually enabled (keeps the volume/data when the instance is terminated)
    - Root EBS volumes are deleted on termination by default
    - Extra non-root EBS volumes are not deleted on termination
- Volume sizes and types can be upgraded without downtime (except for magnetic standard)
- **Elastic Volumes** allow you to increase volume size, adjust performance, or change the volume type while the volume is in use
- To migrate volumes between AZ’s create a snapshot then create a volume in another AZ from the snapshot (possible to change size and type).
- All **volumes** support encryption, but not all **instance types**
- **Throughput optimized EBS** volumes cannot be a boot volume
- You can attach additional EBS volumes to a running instance
    - You cannot decrease an EBS volume size
    - When changing volumes the new volume must be at least the size of the current volume's snapshot
- Images can be made public but not if they're encrypted
- Types
    1. **SSD, General Purpose – gp2/gp3**
        - Volume size from 1 GiB to 16 TiB.
        - Up to 16,000 IOPS per volume.
        - Performance:
            - 3 IOPS/GiB for gp2.
            - Up to 500 IOPS/GiB for gp3.
        - Can be a boot volume.
        -  EBS multi-attach not supported.
        - Use cases:
            - Low-latency interactive apps.
            - Development and test environments.
    2. **SSD, Provisioned IOPS – io1/io2**
        - More than 16,000 IOPS.
        - Up to 64,000 IOPS per volume (Nitro instances).
        - Up to 32,000 IOPS per volume for other instance types.
        - Performance:
            - Up to 50 IOPS/GiB for io1.
            - Up to 500 IOPS/Gib for io2.
        - Can be a boot volume.
        - EBS multi-attach is supported.
        - Use cases:
            - Workloads that require sustained IOPS performance or more than 16,000 IOPS.
            - I/O-intensive database workloads.
    3. **HDD, Throughput Optimized – (st1)**
        - Frequently accessed, throughput intensive workloads with large datasets and large I/O sizes, such as MapReduce, Kafka, log processing, data warehouse, and ETL workloads.
        - Throughput measured in MiB/s and includes the ability to burst up to 250 MiB/s per TB, with a baseline throughput of 40 MB/s per TB and a maximum throughput of 500 MiB/s per volume.
        - Cannot be a boot volume.
        - EBS multi-attach not supported.
    4. **HDD, Cold – (sc1)**
        - Lowest cost storage – cannot be a boot volume.
        - Less frequently accessed workloads with large, cold datasets.
        - These volumes can burst up to 80 MiB/s per TiB, with a baseline throughput of 12 MiB/s.
        - Cannot be a boot volume.
        - EBS multi-attach not supported.

**EBS optimized instances**
- Dedicated capacity for Amazon EBS I/O.
- EBS-optimized instances are designed for use with all EBS volume types.
- Max bandwidth: 400 Mbps – 12000 Mbps.
- IOPS: 3000 – 65000.
- GP-SSD within 10% of baseline and burst performance 99.9% of the time.
- PIOPS within 10% of baseline and burst performance 99.9% of the time.
- Additional hourly fee.
- Available for select instance types.
- Some instance types have EBS-optimized enabled by default.




### Encryption
- You can encrypt both the boot and data volumes of an EC2 instance. WHen you create an ecrypted EBS volume and attach it to a supported instance type, encrypted will be: data at rest, all data moving between the volume and the instance, all snapshots and volumes created from it
- Same IOPS performance of both encrypted and unencrypted volumes
- EBS encrypts your volume with a **AES-256** key
    - Data key is stored on-disk with encrypted data, but not before EBS encrypts it with your CMK

## Instance Store
- Provides temporary (non-persistent) block-level storage for your instance
- Located on the disk that are physically attached to the host computer
- Ideal for temporary storage of information that changes frequently, or a data that is replicated across a fleet of instances, such as load-balancer pool or web servers
- Can specify instance store volumes for an instance only when you launch it
    - You can't detach it and attach to another instance
- The **instance type** determines the size of the instance store available, and the type of hardware used for the instance store volumes
- Good option when needed storage with very low latency, but you don't need the data to persist when the instane terminates, or you can take advantage of fault-tolerant architectures
- High performance and low latency. Usually for distributed/replicated databases that need high I/O
    - The cost of instance store is included in the instance charges so it can also be more **cost-efficient** than EBS Provisioned IOPS
- Instance store backed instances cannot be stopped. If the underlying host fails he data will be lost
- When rebooting the instnaces both **instance store** and **ebs** data will not be lost


## RAID
- Can be used to increase IOPS
- **RAID 0** - stripping - data is written aross multiple disks and icnreases performance but no redundancy
- **RAID 1** - mirroring - creates 2 copies of the data but does not increase performance, only redundancy
- **RAID 10** - combintion of 0 and 1 - resulting in increased performance and redundancy
- **EBS optimized EC2** instances are another way of increasing performance
- Not recommended to use RAID for root/boot volumes


## Amazon Data Lifecycle Manager
- Automates the creation, retention and deletion of EBS snapshots and EBS-bakced AMIs
- Protect valuable data by enforcing a regular backup schedule.
- Create standardized AMIs that can be refreshed at regular intervals.
- Retain backups as required by auditors or internal compliance.
- Reduce storage costs by deleting outdated backups.
- Create disaster recovery backup policies that back up data to isolated accounts.


## FSx (File Server)
- Provides fully managed 
- *Supports Distributed File System Replication DFSR*
- **For windows**: provides a fully managed native Microsoft Windows file system so you can easily move your Windows-based applications that require file storage to AWS. It is built on Windows server
    1. It is a managed Windows Server that runs Windows Server Message Block (SMB)-based file services
    2. Designed for windows
    3. Integrates **Microsoft Active Directory** and can me mounted on your on-premise infrastructure
- **FSx for Lustre** - fully managed file system that is optimized for compute-intensive workloads, such as high-performance computing, ML, media data processing workflows and electronic design automatino (EDA
    1. You can launch and run a Lustre file system that can process massive data sets at up to hundreds of gigabytes per second of throughput, millions of IOPS and sub-milisecond latencies
    2. **When linked to an S3 bucket, objects in **FSx** are presented as files **

**When to use EFS, FSx for windows or FSx for Lustre**
- EFS - when you need distributed, highly resilient storage for Linux instances and linux-based applications
- FSx for windows - when you need centralised storage for windows based applications
- FSx for Lustre - when you need high-speed, high-capacity distributed storage. It can store data directly on S3

__EC2 placement groups__
- When you launch a new EC2 instance, the EC2 service **attempts to place the instance in such a way that all of your instances are spread out across underlying hardware to minimize correlated failures.**
- The name for placement group must be **unique** within your AWS account
- You cant merge placement groups
- You can move existing instance into a placement group. Before you can move the instance, the instsance must be stopped. It *can only be achieved using AWS CLI or SDK*, not yet with a console
1. *Clustered Placement group*
    - Grouping instances within a **single AZ** (put them very close together)
    - Recommented for applications that need **low network latency, high network throughput, or both**
    - Only **certain instances** can be launched in this group
    - Can't span multiple AZ, all other groups can
    - AWS recommneds **homogenous instances** within clustered placement group
2. *Spread Placement group*
    - Each instance is placed on **distinct** underlying hardware
    - Recommended for applications that have a small number of **critical instances that should be kept separate from each other**
        - For example if one applications fails, it should not effect the others
    - Can have in **different AZ within a region**
    - Think like separate instances
3. *Partitioned*
    - AWS divides each group into **logical segments called partitions.**
    - Each partition within a placement group has its own **set of racks**. Each eack has its **own network and power source.**
    - No two partitions within a placement group share the same racks, allowing you to **isolate** the impact of hardware failure within your application
    - Think like multiple instances

__High performance computing (HPC)__
- Create a large number of resources in almost no time. You only pay for the resources you use - and, once finished, you can destroy them
- Can be achieved by these services
1. Data transfer (get data into AWS)
    - *Snowball, SNowmobile* (terabytes/petabytes worth of data)
    - *AWS DataSync* to store on S3, EFS, FSx for Windows
    - *Direct Connect* (cloud service that established a dedicated network connection from your premises to AWS.)
2. Compute and networking
    - CPU or GPU optimized
    - EC2 fleets (Spot instances or fleets)
    - Placement groups (cluster placement groups)
    - Enhanced networking
        1. Uses single root I/O  (SR-IOV)virtualization to provide high-performance networking on supported instance tpypes. Provides higher I/O performance and lower CPU utilization.
        2. Use where you want good network connection
        3. Provies higher bandwidth, higher packet per second performance, consistently lower inter-instance latencies. There is additional charge for this service
        4. Can be enabled using:
            - *Elastic Network Adapter* - supports network speed of up to 100Gbps (Chose during exam)
            - *Virtual Function* interface, supports network speed up to 10 Gbps used for legacy systems
    - Elastic Network Adapters
    - Elastic Fabric Adapters
        1. Is a network device you can attach to your EC2 to accelerate HPC and ML applications
        2. Provides lower, more consistent latency and higher throughput than the TCP protocol.
        3. Can us OS-bypass, allows HPC and ML applications to bypass OS kernel and communicate directly with the EFA device. Only linux
3. Storage
    1. Instance-attached storage
        - EBS
        - Instance Store - scale to millions of IOPS, low latency
    2. Network storage
        - S3
        - EFS
        - FSx
4. Orchestration and automation
    - AWS Batch - batch computing jobs
    - ParallelCluster

__WAF__
- Monitors HTTP and HTTPS requests that are forwarded to CloudFront, ALB or API gateway
- Lets you control access to your content
- Allows three different behaviours:
    1. Allow all requests except the ones you specify
    2. Block all requests except the ones you specify
    3. Count the requests that match the properties you specify

__Storage gateway__
- Hybrid storage between on-premises environments and the AWS Cloud
- Low latency performance by caching frequently accessed data onpremises and storing securely on AWS
- Often used for disaster recovery or backups
- Supports three storage interfaces: file, volume, tape
- Data **in-transit** is always encrypted with SSL
    - All data stored by AWS Storage Gateway in S3 is encrypted server-side
    