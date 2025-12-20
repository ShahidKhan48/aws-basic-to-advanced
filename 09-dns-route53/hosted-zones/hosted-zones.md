# Route 53 Hosted Zones

## 🌐 Hosted Zone Overview

### What is a Hosted Zone?
- **Definition**: Container for DNS records for a particular domain
- **Purpose**: Manage DNS records for your domain
- **Types**: Public and Private hosted zones
- **Name Servers**: AWS provides 4 name servers per hosted zone

## 🌍 Public Hosted Zones

### What are Public Hosted Zones?
- **Definition**: DNS records accessible from the internet
- **Purpose**: Resolve domain names for public websites and services
- **Scope**: Global DNS resolution
- **Cost**: $0.50 per hosted zone per month

### Public Hosted Zone Features
- **Global Reach**: Accessible from anywhere on the internet
- **DNS Resolution**: Standard DNS query resolution
- **Record Types**: All standard DNS record types supported
- **Integration**: Works with domain registrars

### Creating Public Hosted Zone
```bash
# Create hosted zone
aws route53 create-hosted-zone \
    --name example.com \
    --caller-reference $(date +%s)

# Get name servers
aws route53 get-hosted-zone --id Z123456789
```

### Domain Registration Integration
1. **Register Domain**: Use Route 53 or external registrar
2. **Create Hosted Zone**: For your domain
3. **Update Name Servers**: Point domain to Route 53 name servers
4. **Add DNS Records**: Configure your DNS records

## 🏠 Private Hosted Zones

### What are Private Hosted Zones?
- **Definition**: DNS records accessible only within specified VPCs
- **Purpose**: Internal DNS resolution for private resources
- **Scope**: Limited to associated VPCs
- **Security**: Not accessible from the internet

### Private Hosted Zone Features
- **VPC Association**: Associate with one or more VPCs
- **Internal Resolution**: Resolve private IP addresses
- **Split-Horizon DNS**: Same domain name, different records
- **Cross-Account**: Can associate VPCs from different accounts

### Creating Private Hosted Zone
```bash
# Create private hosted zone
aws route53 create-hosted-zone \
    --name internal.example.com \
    --vpc VPCRegion=us-east-1,VPCId=vpc-12345678 \
    --caller-reference $(date +%s)
```

### VPC Association
```json
{
  "HostedZoneId": "Z123456789",
  "VPC": {
    "VPCRegion": "us-east-1",
    "VPCId": "vpc-12345678"
  }
}
```

## 🔧 Hosted Zone Management

### Name Server Configuration
```
Default Name Servers (4 per hosted zone):
ns-123.awsdns-12.com
ns-456.awsdns-45.net
ns-789.awsdns-78.org
ns-012.awsdns-01.co.uk
```

### DNS Delegation
- **Subdomain Delegation**: Delegate subdomains to other hosted zones
- **Cross-Account**: Delegate to hosted zones in other accounts
- **External Delegation**: Delegate to external DNS providers

### Subdomain Delegation Example
```
Parent Zone: example.com
Child Zone: api.example.com

NS Record in Parent Zone:
api.example.com NS ns-123.awsdns-12.com
api.example.com NS ns-456.awsdns-45.net
```

## 🏗️ Split-Horizon DNS

### What is Split-Horizon DNS?
- **Definition**: Different DNS responses based on query source
- **Implementation**: Public and private hosted zones with same domain
- **Use Cases**: Internal vs external services, security, performance

### Split-Horizon Example
```
Public Hosted Zone (example.com):
www.example.com A 203.0.113.1 (Public IP)

Private Hosted Zone (example.com):
www.example.com A 10.0.1.100 (Private IP)
```

### Benefits
- **Security**: Hide internal infrastructure
- **Performance**: Direct internal traffic to private IPs
- **Flexibility**: Different services for internal/external users
- **Cost**: Reduce data transfer costs

## 🔄 Cross-Account Access

### Sharing Private Hosted Zones
1. **Create Hosted Zone**: In account A
2. **Associate VPC**: From account B
3. **Authorization**: Account A authorizes association
4. **Association**: Account B associates VPC

### Cross-Account Association Process
```bash
# Account A: Authorize association
aws route53 create-vpc-association-authorization \
    --hosted-zone-id Z123456789 \
    --vpc VPCRegion=us-east-1,VPCId=vpc-87654321

# Account B: Associate VPC
aws route53 associate-vpc-with-hosted-zone \
    --hosted-zone-id Z123456789 \
    --vpc VPCRegion=us-east-1,VPCId=vpc-87654321
```

## 📊 Monitoring and Logging

### Query Logging
- **CloudWatch Logs**: Log DNS queries
- **Log Groups**: Organize logs by hosted zone
- **Analysis**: Analyze query patterns and performance
- **Cost**: Additional charges for log storage

### Query Log Configuration
```json
{
  "Id": "query-log-config-123",
  "HostedZoneId": "Z123456789",
  "CloudWatchLogsLogGroupArn": "arn:aws:logs:us-east-1:123456789012:log-group:route53-queries"
}
```

### CloudWatch Metrics
- **QueryCount**: Number of DNS queries
- **ResponseTime**: DNS query response time
- **HealthCheckStatus**: Health check results
- **ResolverEndpointRequestCount**: Resolver endpoint metrics

## 💰 Pricing

### Hosted Zone Costs
- **Public Hosted Zone**: $0.50 per month
- **Private Hosted Zone**: $0.50 per month
- **Queries**: $0.40 per million queries (first 1 billion)
- **Health Checks**: $0.50 per health check per month

### Cost Optimization
- **Consolidate Zones**: Use fewer hosted zones when possible
- **Query Optimization**: Optimize DNS query patterns
- **Health Check Optimization**: Use appropriate health check intervals
- **Monitoring**: Monitor usage and costs

## 🎯 Best Practices

### Design
- Use descriptive hosted zone names
- Plan domain structure carefully
- Consider split-horizon DNS for hybrid environments
- Document DNS architecture

### Security
- Use private hosted zones for internal resources
- Implement proper IAM permissions
- Monitor DNS query logs
- Regular security reviews

### Performance
- Use appropriate TTL values
- Optimize query patterns
- Consider geographic distribution
- Monitor response times

### Management
- Use Infrastructure as Code (CloudFormation, Terraform)
- Implement change management processes
- Regular backup of DNS configurations
- Monitor and alert on DNS issues