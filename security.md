__KMS__
- Regional secure key management and encryption and decryption
- Manages customer master keys (CMKs)
- Can encrypt and decrypt data up to 4KB in size
- Pay per API call
- Supports CloudTrail - logs delivered to s3
- FIPS 140-2 Level 2 service - security standard
- There are three types
    1. *Customer Managed* - allows key **rotation**; controlled via key policies and can be enabled/disabled
        - Can view, manage and is dedicated to account
    2. *AWS Managed CMK* - free; used by default if you pifck encryptino in most AWS services; only htta service can use them directly
        - Can see, can't manage and is dedicated to account
    3. *AWS Owned CMK* - Used by AWS on a shared basis across many accounts; you typically won't see these
        - Can't view, can manage, is not dedicated to account
- There are two types of encryption
    1. *Symmetric* - same key used for encryption and decryption
        - Used AES-256 encryption
        - Data never leaves AWS unencrypted
        - Must call the KMS APIs to use
        - Can import your own keys
        - Generate data keys, data key pairs, and random byte strings
    2. *Asymmetric* - mathematically related public/private key pair
        - Based on RSA and ECC algorithms
        - Privtae key never leaves AWS unencrypted
        - Must call the KMS APIs to use private key
        - Download the public key and use outside AWS
        - Used outside AWS by users who can't call KMS APIs
        - Sign messages and verify signatures

__CloudHSM__
- Dedicated hardware security module (HSM)
- Allows you to generate, store and manage cryptographic keys used for data encryption and are accessed only by you
- Helps to meet government standards for secure key management
- Is not exposed outside the cryptographic boundary of the hardware
- Runs on dedicated hardware
- Does not natively integrate with many AWS services like KMS, but instead requires custom application scripting
- FIPS 140-2 level 3
- Manage your own keys
- No access to the AWS-managed component
- Runs within a VPC in your account
- Multi-AZ
- Use when you have strict regulatory compliance requirements


__GuardDuty__
- Intelligent threat detection service
- Continuously monitors for malicious activity and delivers detailed security findings for visibility and remediation
- Monitors AWS accounts, workloads and data in S3


__Systems Manager Parameter Store__
- Component of AWS SYstem Manager (SSM)
- Secure serverless storage for configuration and secrets
- Values can be stored encrypted (KMS) or plaintext
- Can store parameters in **hierarchies**
- Track versions

__Secrets Manager__
- Can automatically rotate secrets

__AWS Shield__
- Protects against DDoS attacks
- Two types
    1. *AWS Shield standard* - automatically enabled for all customers at no cost
        - Protects against common layer 3 and 4 attacks
            - SYN/UDP floods
            - Reflection attacks
    2. *AWS Shield Advanced* - 3000 dollars per month, per organisation
        - Enhanced protection for EC2, ELB, CloudFront, Global Accelerator, Route 53
        - 24*7 access to the DDoS Response Team
        - DDoS cost protection

__Inspector__
- Is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS
- Assesses applications for exposure, vulnerabilities and deviations from best practices
- Helps to check for unintended network accessibility of your EC2 instances and for vulnerabilities on those instances
- Produces a detailed list of security findings that is organized by level of severity
- Can install **Inspector Agent** on EC2 instances for more detailed scanning


__AWS Glue__
- Pay-as-you-go, extract, transform and load service that automates the time-consuming steps of data preparation for analytics
- Is used to discover properties of data, transform it, and prepare it for analytics
- Can automatically discover both structured and semi-structured data stored in data lakes on S3 and RedShift
- Transform and move data to various destinations. Used to prepare and load data for analytics. Data source can be S3, RedShift or another databse. 

__Incognito__
- Lets user sing-ip and up and access control to your web and mobile apps quickly and easily
- You can use directly with user name and passsword, or through third party like facebook
- Works with SAML, OpenID, social identity providers (facebook)
- The user authenticates first with the Web ID provider and receives an authentication token, which is then exchanges for temporary AWS credentials allowing them to assume an IAM role allowing access to the required resources.
- Two main components are:
    1. *Users pools* - are user directories that provide sign-up and sign-in options for your app users
    2. *Identity pools* - enable you to grant your users access to other AWS services. **Temporary security credentials**
    You can use them separately or together
- It exposes server-side APIs
- There is an import tool for migrating users into an Cognito User Pool
- Process: User authenticates and gets token from *Cognito User pool*, then exchangers tokens for aws credentials from *Cognito identity pool*
- Exam tip: To make it easier to remember the different between User Pools and Identity Pools, think of Users Pools as being like IAM Users or Active Directory and an Identity Pools as being like an IAM Role.
- *Cognito Sync* - enables cross-device syncing of application-related user data
- Can use to synchronize user profile data across mobile devices and the web without requiring your own backend
- When the device is online, you can synchronize data, and if you set up push sync, notify other devices immediately that an update is available.
- Exam tip: AWS AppSync is a similar service that has additional capabilities. With AppSync you can synchronize mobile app data across devices and users (Cognito Sync cannot synchronize across users, only devices), it has support for additional devices and data types, and is based on GraphQL.

__CodeCommit__
- AWS CodeCommit is a secure, highly scalable, managed source control service that hosts private Git repositories. It makes it easy for teams to securely collaborate on code with contributions encrypted in transit and at rest.


__Step Function__
- AWS Step Functions is a low-code, visual workflow service that developers use to build distributed applications, automate IT and business processes, and build data and machine learning pipelines using AWS services. Workflows manage failures, retries, parallelization, service integrations, and observability so developers can focus on higher-value business logic.