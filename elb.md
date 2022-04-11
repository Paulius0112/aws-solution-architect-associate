__Elastic Load Balancer__
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





















