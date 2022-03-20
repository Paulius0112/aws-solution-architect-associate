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
- FIPS 140-2 level 3
- Manage your own keys
- No access to the AWS-managed component
- Runs within a VPC in your account
- Multi-AZ
- Use when you have strict regulatory compliance requirements


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