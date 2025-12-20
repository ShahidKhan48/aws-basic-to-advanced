# DNS Record Types - A, CNAME & ALIAS

## 📝 DNS Record Types Overview

### Common Record Types
- **A Record**: Maps domain to IPv4 address
- **AAAA Record**: Maps domain to IPv6 address
- **CNAME Record**: Maps domain to another domain name
- **ALIAS Record**: AWS-specific record type
- **MX Record**: Mail exchange servers
- **TXT Record**: Text information
- **NS Record**: Name server records
- **SOA Record**: Start of authority

## 🅰️ A Records

### What is an A Record?
- **Definition**: Address record mapping domain to IPv4 address
- **Purpose**: Direct domain name to IP address
- **Format**: Domain name → IPv4 address
- **TTL**: Time to live for caching

### A Record Examples
```
www.example.com    A    203.0.113.1
api.example.com    A    203.0.113.2
blog.example.com   A    203.0.113.3
```

### A Record Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "TTL": 300,
  "ResourceRecords": [
    {
      "Value": "203.0.113.1"
    }
  ]
}
```

### A Record Use Cases
- **Web Servers**: Point domain to web server IP
- **API Endpoints**: Direct API calls to server
- **Load Balancers**: Point to load balancer IP
- **CDN**: Point to CDN edge locations

## 🔗 CNAME Records

### What is a CNAME Record?
- **Definition**: Canonical Name record mapping domain to another domain
- **Purpose**: Create aliases for domain names
- **Limitation**: Cannot be used for root domain (apex)
- **Chain**: Can create chains of CNAME records

### CNAME Record Examples
```
www.example.com     CNAME    example.com
blog.example.com    CNAME    wordpress.example.com
cdn.example.com     CNAME    d123456.cloudfront.net
```

### CNAME Record Configuration
```json
{
  "Name": "www.example.com",
  "Type": "CNAME",
  "TTL": 300,
  "ResourceRecords": [
    {
      "Value": "example.com"
    }
  ]
}
```

### CNAME Limitations
- **Root Domain**: Cannot use CNAME for root domain (example.com)
- **Other Records**: Cannot coexist with other record types for same name
- **Performance**: Additional DNS lookup required
- **Chain Length**: Avoid long CNAME chains

## ⭐ ALIAS Records (AWS Specific)

### What is an ALIAS Record?
- **Definition**: AWS-specific record type for AWS resources
- **Purpose**: Point domain to AWS resources without IP addresses
- **Benefits**: No additional DNS lookup, supports root domain
- **Cost**: No charge for ALIAS queries to AWS resources

### ALIAS vs CNAME
| Feature | ALIAS | CNAME |
|---------|-------|-------|
| Root Domain | ✅ Supported | ❌ Not Supported |
| Additional Lookup | ❌ No | ✅ Yes |
| AWS Resources | ✅ Optimized | ⚠️ Basic |
| Cost | 🆓 Free for AWS | 💰 Charged |
| Coexistence | ✅ With other records | ❌ Exclusive |

### ALIAS Record Examples
```
example.com         ALIAS    d123456.cloudfront.net
www.example.com     ALIAS    my-alb-123456.us-east-1.elb.amazonaws.com
api.example.com     ALIAS    my-nlb-789012.elb.us-east-1.amazonaws.com
```

### ALIAS Record Configuration
```json
{
  "Name": "example.com",
  "Type": "A",
  "AliasTarget": {
    "DNSName": "d123456.cloudfront.net",
    "EvaluateTargetHealth": true,
    "HostedZoneId": "Z2FDTNDATAQYW2"
  }
}
```

### Supported AWS Resources
- **CloudFront Distributions**
- **Elastic Load Balancers** (ALB, NLB, CLB)
- **API Gateway**
- **S3 Website Endpoints**
- **VPC Endpoints**
- **AWS Global Accelerator**

## 📧 Other Important Record Types

### MX Records (Mail Exchange)
```
example.com    MX    10    mail1.example.com
example.com    MX    20    mail2.example.com
```

### TXT Records (Text)
```
example.com              TXT    "v=spf1 include:_spf.google.com ~all"
_dmarc.example.com       TXT    "v=DMARC1; p=quarantine; rua=mailto:dmarc@example.com"
```

### NS Records (Name Server)
```
subdomain.example.com    NS     ns1.subdomain-provider.com
subdomain.example.com    NS     ns2.subdomain-provider.com
```

### SRV Records (Service)
```
_sip._tcp.example.com    SRV    10 5 5060 sip1.example.com
_sip._tcp.example.com    SRV    10 5 5060 sip2.example.com
```

## 🔧 Record Management

### TTL (Time To Live)
- **Purpose**: Control DNS caching duration
- **Range**: 0 to 2,147,483,647 seconds
- **Common Values**:
  - 300 seconds (5 minutes): Frequent changes
  - 3600 seconds (1 hour): Standard
  - 86400 seconds (24 hours): Stable records

### TTL Considerations
```
Short TTL (300s):
✅ Quick changes
❌ More DNS queries
❌ Higher costs

Long TTL (86400s):
✅ Fewer DNS queries
✅ Lower costs
❌ Slower changes
```

### Record Sets
- **Multiple Values**: Multiple IP addresses for same record
- **Load Distribution**: Simple load balancing
- **Redundancy**: Failover capability

### Multi-Value Record Example
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.1"},
    {"Value": "203.0.113.2"},
    {"Value": "203.0.113.3"}
  ]
}
```

## 🎯 Best Practices

### Record Type Selection
- **Use A Records**: For direct IP mapping
- **Use ALIAS**: For AWS resources (preferred over CNAME)
- **Use CNAME**: For non-AWS aliases (not root domain)
- **Avoid CNAME Chains**: Keep redirections simple

### TTL Management
- **Start Low**: Use low TTL during testing/migration
- **Increase Gradually**: Raise TTL for stable records
- **Consider Impact**: Balance between performance and flexibility
- **Monitor Changes**: Track DNS propagation

### Performance Optimization
- **Minimize Lookups**: Avoid unnecessary CNAME chains
- **Use ALIAS**: For AWS resources to reduce lookups
- **Appropriate TTL**: Balance caching and change frequency
- **Geographic Distribution**: Consider global DNS performance

### Security Considerations
- **Validate Records**: Ensure records point to correct resources
- **Monitor Changes**: Track DNS record modifications
- **Access Control**: Implement proper IAM permissions
- **Regular Audits**: Review DNS configurations periodically