- *Tight coupling* -  
- *Loose coupling*


__SQS__
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


__Simple Work Flow__
- Service that makes it easy to coordinate work across distributed application components
- Tasks represent invocations of various processing steps in an application which can be performed by executable code, web service calls, human actions, scripts
- IT tasks + Human actions
- Workflow execution can last up to 1 year
- Tasks assigned only once and never duplicated
- Tracks all events and tasks in an application

__SNS__
- Manage notifications form the cloud
- Can deliver messages to mobile devices, SMS text, email, SQS, HTTP endpoint
- Groups multiple recipients using *topics*
    - Requires recipients to subscribe to the topic
- All messages are published to Amazon SNS are stored redundantly across multple AZ
- Benefits
    - Instantaneous, push-based delivery (no pooling)
    - Flexible message delivery over multiple transport protocols
    - Pay as you go

__API gateway__
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


__Kinesis__
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


__Web Identity Federation__ - *Amazon Cognito*
- Lets you give your users access to AWS resources after they have successfully authenticated with a web-based identity provider like Amazon, Google..


__Event Processing Patterns__
- pub/sub with SNS
- *Dead-Letter queue* - can be used by SNS, SQS, Lambda 
- *S3 Event notifications*
    1. **s3:objectCreated:Put, s3:ObjectCreated:\***
    2. **Object Removed** - supports deletes of versioned and unversioned objects
    3. **Object Restored** - restoration of objects in Glacier
    4. **Replication** - on failed, exceeded 15 minutes or object no longer tracked by replication metrics
