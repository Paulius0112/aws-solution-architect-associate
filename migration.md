# Server Migration Service
- Migrates thousands of on-premises workloads to AWS
- Allows to automate, schedule and track incremental replications of live server volumes, making it easier to control large-scale server migrations
- *AWS Transfer Family* provides fully managed support for file transfers directly into and out of Amazon S3 or EFS


- **Backup strategies**
    1. Backup & Restore
        - RPO / RTO - hours
        - Provision all AWS resources after event
        - Restore backups after event
        - Cost lowest
    2. Pilot Light
        - RPO / RTP 10s of minutes
        - Data live
        - Services idle
        - Provision some AWS resources and scale after event
        - More costlier
    3. Warm Standby
        - RPO / RTO - minutes
        - Alwtays running, but smaller
        - Business critical
        - Scale aws resources after event
        - third mostg costly
    4. Multi-site active
        - RPO / RTO - zero downtime
        - near zero data loss
        - Mission critical services
        - most expensive