__Route 53__
- *SOA* - start of authority record. The name of the server that supplied the data for the zone, administrator, TTL
- *NS* - name server records. They are used by Top Level Domain servers to direct traffic to the Content DNS server which contains the authoritative DNS records.
- *A* record - is used to translate the name of the domain to an IP address
- *TTL* - cache of records
- *CName* - can be used to resolve one domain name to another. It must have either an A record or an Alias
- *Alias* records - are used to map resource record sets in your hosted zone to ELB, Cloudfront, S3. They work like CNAME
