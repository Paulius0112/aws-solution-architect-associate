__AWS Directory Service__
- Connect AWS resources with on-premises AD
- Standalone direcotory in the cloud
- Use exsiting corporate credentials
- SSO to anu domain-joined EC2 instance
- Can assign *Group policies*
- Based on two protocols
    1. LDAP (Lightweight directory access protocl)
    2. DNS


# IAM programmatic access
- Can be granted using **Query API** or **Access key ID and secret key**

__AWS Managed Microsoft AD__
- Responsibilities
    1. AWS
        - Multi-AZ deployment
        - Patch, monitor, recover
        - Instance rotation (latest software version)
        - Snapshots and backups
    2. Customer
        - Users, groups, group policies
        - Standard AD tools
        - Scale out domain controller
        - Trusts (resource forest
        - Manage certificate authorities
        - Federation

__Simple AD__
- Simple user directory, or you need LDAP compatibility
- Standalone managed directory
- Basic AD features
- There are two sizes: Small <= 500; Large <= 5k users
- They dont support *trusts* (can't join on-premises AD)

__AD connector__
- Directory gateway (proxy) for on-premises AD
- Avoid caching information in the cloud
- Allow in-premises users to log in to AWS using AD
- Scale across multiple AD connectors
- Must have an existing AD

__Cloud directory__
- Directory-based store for developers
- Use cases: org charts, course catalogs, device registries

__Amazon Cognito User Pools__
- Managed user directory for Saas applications
- Sign-up and sign-in for web or mobile
- Works with social media identities

__AD vs Non-AD compatible services__
- Compatible
    1. Managed Microsoft AD
    2. AD connector
    3. Simple AD
- Non compatible
    1. Cloud directory
    2. Cognito user pools


__Service Control Policies__
- Allow to manage your permissions in your organization
- SCPs aren't available if your organization has enabled only the consolidated billing features
- It does not guarante any permissions, only set limits on the actions that the account's administrator can delegate to the IAM users and roles
- Affect only IAM users and roles, but don't affect resource-based policies directly.


__IAM policies__
- ARNs all begin with
    - `arn:partition:service:region:account_id:`
- JSON document that defines permissions
- Two types
    1. Identity policy - attached to IAM User, group or role. Permissions
    2. Resource policy - attached to resources
- No effect until attached
- Structured as list of statements (each statement maches an AWS API request)
- Can either *allow* or *deny* specific actions of specific resource
- AWS joins all applicable policies
- Explicit deny overrides everything else
- *Permissions boundaries for IAM entities* 
    - Used to delegate administration to other users
    - Control maximum permissions an IAM policy can grant
    - Use cases:
        - Developers creating roles for lambda functions
        - Application owners creating roles for EC2 instances
        - Admins creating ad hoc users
- You can set *password policy* for each user in AWS account


__AWS Resource Access Manager (RAM)__
- Allows resource sharing between accounts

__AWS SSO__
- Service that helps centrally manage access to AWS accounts and business applications
- SAML (Security Assertion Markup Lnaguage) relates with SSO



__IAM Groups__
- They are not an identity


__Amazon Cognito__ has two __authentication methods__, __independent__ of one another —

- Sign in via third party federation
- Cognito user pools

__AWS Directory Service__ options —

- AWS Managed Microsoft AD
- AD Connector
- Simple AD
- Amazon Cloud Directory
- Amazon Cognito

There is no __default policy__ ever, anywhere. When permissions are checked, roles and policies are considered together, and in the default case there is no policy, so only the role is considered.

We can configure __IAM policies__ that allows __access to specific tags__.

__Connecting AWS SSO to On-Premise Active Directory__ —

- __Two-way trust relationship__: __Preferred__. Users can do everything from both portals.
- __AD connector__: SSO does not cache user credentials. Users can't reset password from SSO portal, have to do it from on-premise portal.

For __two-step verification__, SSO sends __code to registered email__. It can set to be either —

- Always-on
- Context-aware

__Cross-account IAM roles__ allow customers to securely grant access to AWS resources in their account to a third party.

If our identity store is not compatible with SAML, we can develop a custom application on-premise and use it with STS.

__Microsoft Active Directory__ supports __SAML__. 
