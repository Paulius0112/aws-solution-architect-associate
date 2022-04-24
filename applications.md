# SQS

- Web service that gives you access to a message queue that can be used to store messages while waiting for a computer to process them
- Is a temporary repository for messages that are awaiting processing
- **Decoupled** infrastructure
- Messages can contain up to 256KB of text in any format (its not a hard limit)
- *Pull-based* - need to have a consumer
- Messages are kept from 1 minutes to 14 days; the default retention period is 4 days
- Pooling based
- Two types:
    1. *Standard queues (default)* - provides nearly unlimited number of transactions per second. 
        - Guarantee that a meesage is delivered at least once
        - Is not guarenteed that messages are delivered in the same order as were received
        - Also, multiple copies of data can be transmitted
    2. *FIFO queues*
        - FIFO delivery and exactly one processing
        - No duplicates
        - Limited to 300 transactions per second
        - Slower than Standard
- *Visibility timeout* - time that the message is invisible in the SQS queue after a reader picks up that message
    - If the message was processed within time, it will be deleted from the queue
    - If the message was not processed within time, it will become visible in the queue. This could result in the same message delivered twice
    - Maximum is 12 hours
- *Long polling* is a way to retrieve messages from SQS. While *regular short pooling* returns immediately (even if queue is empty), *long pooling* doesn't return a response until a message arrives in the SQS or the long pool times out
    - Long polling saves money


# Simple Work Flow

- Service that makes it easy to coordinate work across distributed application components
- Tasks represent invocations of various processing steps in an application which can be performed by executable code, web service calls, human actions, scripts
- IT tasks + Human actions
- Workflow execution can last up to 1 year
- Tasks assigned only once and never duplicated
- Tracks all events and tasks in an application

# SNS

- Manage notifications form the cloud
- Can deliver messages to mobile devices, SMS text, email, SQS, HTTP endpoint
- Groups multiple recipients using *topics*
    - Requires recipients to subscribe to the topic
- All messages are published to Amazon SNS are stored redundantly across multple AZ
- Benefits
    - Instantaneous, push-based delivery (no pooling)
    - Flexible message delivery over multiple transport protocols
    - Pay as you go

# API gateway

- Supports:
    1. Creating and deploying REST API to expose HTTP endpoints, AWS Lambda functions and other services
    2. Web Sockets
    3. Invoke exposed API methods
- Infrastructure architecture
    1. Together with lambda, **API Gateway** forms the app-facing part of the AWS serverless
    2. Back-end services include EC2, Lambda or any web application (public or private endpoint)
- Provides REST API that uses JSON and exposes HTTPS endpoint (and only HTTPS)
- Can have a certificate and send each endpoint to a different target
- CloudFront is used as the public endpoint for API gateway
- By default API gateway assigns an internal domain that automatically uses the API gateway certififcates
- *Endpoint types*
    1. **Edge-Optimized Endpoint** - best for geographical distributed clients (default type). Is used
    across all regions and capitalize the name of HTTP headers
    2. **Regional Endpoint** - intended for clients in the same region. 
        - When a client on EC2 instance calls an API in the same region, or when an API is intended to serve a small number of clients with high demand. Reduced connection overhead
    3. **Private endpoint** - can only be accessed from VPC using an interface VPC endpoint, which is an endpoint network interface (ENI) that you create in your VPC
- *API gateway API's*
    1. **REST API** - collection of HTTP resources and methods that are integrated with backend HTTP endpoints.
        - Can be deployed in one or more stages
    2. **WebSocket API** - collection of WebSocket routes and route keys that are integrated with backend HTTP endpoint
- *Deployments* are a snapshot of the APIs resources and methods. They must be created and associated with a stage for anyone to access the API
- *Stages and Stage variables*
    - *Stage* is a logical reference to a lifecycle state of your REST or WebSocket api
    - API stages are identified by API ID and stage name
    - Stage variables are like environment variables
    - Variables can be used to pass configuration parameters to AWS lambda through mapping templates
- *Caching*
    - You can add caching to API calls by provisioning an Amazon API gateway cache and specifying its size in gigabytes
    - Allows to cache endpoint's response
    - Reduces the number of calls to the backend and improves latency
    - Default TTL is 300 seconds while maximum is 3600
    - Caches are defined by stage
    - You can encrypt caches
    - You can override cache settings for specific methods and can invalidate entire cache
    - Clients can invalidate the cache with the header: *Cache-Control: max-age=0*
- *API throttling*
    - API sets a limit on a steady-state rate and a burst of request submissions against all APIs in your account
    - Limits:
        - By default, API gateway limits *10000 requests per second*
        - Maximum concurent requests is 5000 requests across all APIs
        - If you go over the limit, you will receive *429 Too Many Requests*
    - *Two types of throttling*:
        - **Server side** - limits are applied across all clients. These limit settings exist to prevent your API from being overwhelmed by too many requests
        - **Per-client** - limits are applied to clients that use API keys associated with your usage policy as a client identifier
- *Usage plans and API keys*
    - Specifies who can access one or more deployed API stages and methods, and how much and how fast they can access them
    - You can use them to configure throttling and quota limits
- Expose HTTPS endpoints to define a RESTful API
- Connect to other services
- Scale effortlessly (automatically)
- Throttle requests to prevent attacks
- Maintain multiple versions of your API
- Supports SSL/TLS
- Can enable API *caching* to cache endpoint response
    - Improves latency and reduces the number of calls made to your endpoint
- *Same-origin policy* - a web browser permits scripts contained in a first web page to access data in a second web page, but only if both web pages have the same origin (domain name)
    - Prevents Cross-site scripting attacks
    - *CORS* enable it
        - Allows restricted resources (such as fonts) on a web page to be requested from another domain outside the domain from which the first resource was served

# Kinesis

- Kinesis data stream is a set of shards. Each **shard** has a sequence of data records. Rach data record has a **sequence number** that is assigned by Kinesis Data Stream
- *Data record* - is the unit of data stored in a Kinesis data stream. It is composed of a sequence number, a partition key and data blob.
- *Partition key* - is used to group data by shard within a stream. Kinesis segregates the data records belonging to a stream into multiple shards. 
    - It uses **partition key** that is associated with each data record to determine which shard a given data record belongs to.
- There are three types:
    1. *Kinesis Streams* - allows persistantly store data until consumers take the data out
        - By default, data is tored for 24 hours, but can be configure for up to 7 days
        - Use when need data persistance
        - Data contained in *shards*
            - The more shards, the higher capacity of stream
    2. *Kinesis Firehose* - no data persistance. You need to do something with the data once it is received in Firehose, such as integrate a Lambda
    3. *Kinesis Analytics* - works with Kinesis Streams and Firehose - analyses data in those streams
- *


# Web Identity Federation - Amazon Cognito
- Lets you give your users access to AWS resources after they have successfully authenticated with a web-based identity provider like Amazon, Google..


# Event Processing Patterns
- pub/sub with SNS
- *Dead-Letter queue* - can be used by SNS, SQS, Lambda 
- *S3 Event notifications*
    1. **s3:objectCreated:Put, s3:ObjectCreated:\***
    2. **Object Removed** - supports deletes of versioned and unversioned objects
    3. **Object Restored** - restoration of objects in Glacier
    4. **Replication** - on failed, exceeded 15 minutes or object no longer tracked by replication metrics

# Step function
- orchestrates serverless workflows including coordination, state, and function chaining as well as combining long-running executions not supported within Lambda execution limits by breaking into multiple steps or by calling workers running on EC2 instances or on-premise

# AWS Batch
- Allows to run single jobs that span multiple EC2 instances. 
- Can run large-scale, tightly coupled, HPC
- Dont need to launch and manage EC2 instance directly - managed service
- Used with **MPI Message Passing Interface**

# AWS Secure Token Service STS
- Enables to request temporary, limited-privilege credentials for IAM users or for users that you authenticate (such as federated users from an on-premise directory)
- Federation (typically Active Directory) uses **SAML 2.0** for authentication and grants temporary access based on the users AD credentials. The user does not need to be a user in IAM

# Elastic Load Balancer
- Automatically distributes incoming application traffic across multiple targets. Can be done across *multiple AZs*
- Three types
    1. *Application Load Balancer* - best suited for load balancing of HTTP and HTTPS traffic. Layer 7. ROutes based on the content of the request
        - Once request is received, ALB evaluates the listener rules in priority order to determine which rule to apply, and then selects a target from the target group for the rule action
        - A **listener** checks for connection requests from clients, using the protocol and port you configure
            - A **listener** points to **target group**
            - A target group has a **health checks**
        - Each **rule** consists of a priority, one or more actions, and one or more conditions
        - **Sticky sessions** are available, but the traffic will be sent at the target group level. Always directs to the same server
        - At least two subnets must be specified
    2. *Network Load Balancer* - handles milions of requests per second, while maintaining ultra-low latencies. Layer 4. Performance load balancer. Routes based on IP protocol data
        - Supported protocols are **TCP, TLS, UDP, TCP_UDP**
        - Ports available **1-65535**
        - **Encryption** - can use TLS listener to encrypt/decrypt your LB (will need certificate)
        - Supports *cross-zone* load balancing, but is not enabled by default.
            - Without it, NLB will distribute traffic 50/50. If there are odd number of EC2 instances in AZs, cross-zone will be required to distribute traffic to all instances evently
        - At least one subnet must be specified
    3. *Classic Load Balancer* - legacy load balancer. Can load HTTP/HTTPS and Layer 7. Can also use layer 4
        - Provides **X-Forwarded-For** - the server access logs contain IP address only of a proxy or load balancer. This header shows the origin IP of the client (external IP address)
        - Provides **sticky sessions** - allows to bind a user's session to a specific EC2 instace
            - Can be useful if you are storing informatino locally to that instance
        - **Gateway timeouts** - 504 application stoped responding.
- ELB nodes have public IPs and rpoute traffic to the private IP addresses of the Ec2 instances. You need one public subnet in each AZ where the ELB is defined and the private subnets are located
- Targets can be EC2, containers, IP addresses and lambda functions
- Only one subnet per AZ can be enabled for each ELB
- Route53 can be used for region load balancing with ELB instances configured in each region
- Can be *internet facing* or *internal-only*
- Uses DNS record TTL of 60 seconds
- *Perfect Forward Secrecy PFS* - provides additional safeguard against the eavesdropping of encrypted data, using a unique session key
- Provides security against DDoS
- Deleting an ELB does not affect the instances registered against it (they wont be deleted, they just wont receive any more requests)
- *Health Checks*
    - Periodically send requests to load balancer's registered instances to test their status
    - Healthy status is **InService**
    - Unhealthy status is **OutOfService**
    - Load balancer sends normal requests only to **healthy** targets
- Can have **path patterns** - depending on the url, can route to different target group in the same or different AZ
- ALB only support HTTP/HTTPS
- You must have at least one SSL/TLS server certificate on your load balancer to use HTTPS listener


__Deregistration delay__
- Allows LB to keep existing connections open if the EC2 instances are de-registered or become unhealthy
- This enables the LB to complete in-flight requests made to instances that are de-registering ir unhealthy
- You can disable deregistration delay if you want your LB to immediately close connections to the instances that are de-registering or have become unhealthy


# CloudFormation
- Manages infrastructure as a code
- Resources are defined using Cloudformation *template*
- Supports YAML and JSON
| CloudFormation | Elastic Beanstalk |
|----------------|-------------------|
| Deploys IaC | Deployes applications on EC2 (PaaS) |
| Can be used to deploy almost any AWS service | Deployes web applications |
| Uses JSON or YAML | Uses ZIP or WAR files |
- Benefits:
    1. Infrastructure is provisioned consistently, with fewer mistakes (human error)
    2. Less time and effort than configuring resources manually
    3. Can use version control and peer review
    4. Manage update and dependencies
    5. Can rollback and delete the entire stack
- Key concepts:
    - **Templates** - JSON or YAML file containing code
    - **Stacks** - the entire environment described by the template and created, updated, and deleted as a single unit
    - **StackSets** - extends the functionality of stacks by enabling you to create, update, delete stacks across multiple accounts and regions with a single operation
    - **Change sets** - A summary of proposed changes to your stack that will allow you to see how those changes might impact your existing resources before implemenint them
- **Templates**:
    - After creating, you upload it to CloudFormation directly or using S3
    - The resulting resources are called *stack*
    - **Logical IDs** are used to reference resources within the template
    - **Physical IDs** identify resources outside of AWS cloudformation template, but only after the resources have been created
- **Stacks**:
    - Deployed resources based on templates
    - Create, update, delete stacks using templates
    - Stack creation errors:
        1. Automatic rollback on error is enabled by default
        2. You will be charged for resources provisioned even if there is an error
    - Updating stacks:
        1. CloudFormation provides two methods for updating stacks: direct update or creating and executing change sets
        2. WHen you directly update a stack, you submit changes and CloudFormation immediately deploys them. 
        3. Use direct updates when you want to quickly deploy your changes
        4. With change sets, you can preview the changes CloudFormation will make to your stack, and then decide whether to apply those changes
- **Stack Sets**
    - Extends functionality and allows to create, update, delete stacks across multiple accounts and regions with a single operation
    - A stack set is managed by signing in to the AWS administrstor account in which it was created
- **Nested stacks**
    - Allow to re-use of cloudformation code for common use cases
        - For example, standard configuration for a load balancer, web server, application server etc.
        -  Instead of copying the code, create a standard template for each common use case and reference from within your cloudformation template
- Best practises
    - Make use of *change sets* to identify potential problems in your updates
    - Use stack policies to explicitly protect sensitive portions of your stack
    - Use version control systems such as *CodeCommit* or *Github* to track changes to templates
- **Serverless Application Model**
    - Use SAM for deploying serverless applications using cloudformation
    - SAM is an extension to cloudformation used to define serverless applications
    - Provides simplified syntax for defining serverless resources
- **Rollbacks and creation failures**
    - Stack creation failures:
        1. By default everything will be deleted
        2. You can optionally disable rollback (for good troubleshooting failures)
    - Stack update failures
        1. The stack will automatically roll back to the previous knonw working state
        2. The logs can assist with understanding what issue occured
- **Monitoring and reporting**
    - You can monitor the progress of a stack update by viewing the stack's events

# Elastic Beanstalk
- Developers upload applications and beanstalk handles the deployment details of capacity provisioning, load balancing, auto-scaling and application health monitoring
- Can use multi-AZ
- Considered as Platform as a Service (PaaS)
- Automatically scales application
- You can select EC2 type
- Can retain full administrative control or have Beanstalk do it for you
- The *Managed Platform Updates* feature automatically applies updates for your OS and platform
- Monitors and manages application health and information is viewable cia a dashboard
- Integrates with CloudWatch and X-Ray
- Integrates with VPC and AWS IAM
- Multiple environments are supported to enable versioning
- Code is deployed using WAR file or Git repository
- By default applications are publicly accessible
- Can access logs without logging into application servers
- **Layers**
    - *Application* - is a collection of different elements, such as environments, configurations and versions
        - You can have multiple application versions held within an application
    - *Application version* - is a very specific reference to a section of deployable code
        - The application version will point typically to an Amazon S3 bukcet containing the code
    - *Environment* - Application version that has been deployed on AWS resources
        - It is comprised of all the resources created by Beanstalk and not just an EC2 instance with your uploaded code
    - *Environment tier* - determines how EB provisions resources based on what the application is designed to do
        - **Web servers** - are standard applications that lsiten for and then process HTTP requests, typically over port 80
        - **Workers** - are specialized applications that have a background processing task that listens for messsages on Amazon SQS queue
    - *Configuration template* - template that provides the baseline for creating a new, unique environment configuration
- **Deployment options**:
    - *All at once*
    - *Rolling*
    - *Rolling with additional batch*
    - *immutable*