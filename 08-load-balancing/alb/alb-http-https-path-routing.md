# ALB - HTTP/HTTPS & Path Routing

## 🌐 Application Load Balancer (ALB) Overview

### What is ALB?
- **Definition**: Layer 7 (Application) load balancer
- **Protocols**: HTTP, HTTPS, HTTP/2, WebSocket
- **Purpose**: Distribute incoming application traffic across multiple targets
- **Intelligence**: Content-based routing decisions

## 🔧 ALB Features

### Layer 7 Capabilities
- **HTTP Headers**: Route based on HTTP headers
- **Request Path**: Path-based routing
- **Query Parameters**: Route based on query strings
- **Host Headers**: Host-based routing
- **HTTP Methods**: Route based on GET, POST, etc.

### Target Types
- **EC2 Instances**: Direct instance targeting
- **IP Addresses**: Target specific IP addresses
- **Lambda Functions**: Serverless function integration
- **Application Load Balancers**: ALB chaining

## 🛣️ Path-Based Routing

### What is Path-Based Routing?
- **Definition**: Route requests based on URL path
- **Use Cases**: Microservices, API versioning, content separation
- **Rules**: Multiple path patterns per listener
- **Priority**: Rules processed in priority order

### Path Routing Examples
```
/api/v1/* → API Service Target Group
/images/* → Image Service Target Group
/admin/*  → Admin Service Target Group
/*        → Default Target Group (catch-all)
```

### Routing Rule Configuration
```json
{
  "Rules": [
    {
      "Priority": 1,
      "Conditions": [
        {
          "Field": "path-pattern",
          "Values": ["/api/v1/*"]
        }
      ],
      "Actions": [
        {
          "Type": "forward",
          "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/api-targets/1234567890123456"
        }
      ]
    }
  ]
}
```

## 🎯 Target Groups

### What are Target Groups?
- **Definition**: Logical grouping of targets for load balancing
- **Health Checks**: Monitor target health
- **Routing**: ALB routes requests to healthy targets
- **Protocols**: HTTP, HTTPS for ALB

### Target Group Configuration
- **Protocol**: HTTP or HTTPS
- **Port**: Target port number
- **Health Check Path**: URL path for health checks
- **Health Check Interval**: Frequency of health checks
- **Healthy/Unhealthy Thresholds**: Number of checks to determine status

### Health Check Settings
```
Protocol: HTTP
Port: 80
Path: /health
Interval: 30 seconds
Timeout: 5 seconds
Healthy Threshold: 2
Unhealthy Threshold: 5
```

## 🔒 SSL/TLS Termination

### SSL Termination
- **Definition**: ALB handles SSL/TLS encryption/decryption
- **Benefits**: Offload SSL processing from backend servers
- **Certificates**: AWS Certificate Manager (ACM) integration
- **Protocols**: TLS 1.0, 1.1, 1.2, 1.3

### Certificate Management
- **ACM Integration**: Free SSL certificates
- **Custom Certificates**: Upload your own certificates
- **SNI Support**: Multiple certificates per load balancer
- **Automatic Renewal**: ACM handles certificate renewal

### SSL Policy Example
```
ELBSecurityPolicy-TLS-1-2-2017-01:
- TLS 1.2 only
- Strong cipher suites
- Perfect Forward Secrecy

ELBSecurityPolicy-FS-1-2-Res-2020-10:
- TLS 1.2 only
- Forward Secrecy required
- Restricted cipher suites
```

## 🔄 Advanced Routing

### Host-Based Routing
```
api.example.com → API Target Group
web.example.com → Web Target Group
admin.example.com → Admin Target Group
```

### Header-Based Routing
```
User-Agent: Mobile → Mobile Target Group
X-Forwarded-Proto: https → Secure Target Group
```

### Query Parameter Routing
```
?version=v1 → V1 Target Group
?version=v2 → V2 Target Group
```

## 🔧 ALB Configuration

### Listener Configuration
```json
{
  "Protocol": "HTTPS",
  "Port": 443,
  "SslPolicy": "ELBSecurityPolicy-TLS-1-2-2017-01",
  "Certificates": [
    {
      "CertificateArn": "arn:aws:acm:us-east-1:123456789012:certificate/12345678-1234-1234-1234-123456789012"
    }
  ],
  "DefaultActions": [
    {
      "Type": "forward",
      "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/default-targets/1234567890123456"
    }
  ]
}
```

### Security Groups
```
Inbound Rules:
- HTTP (80) from 0.0.0.0/0
- HTTPS (443) from 0.0.0.0/0

Outbound Rules:
- HTTP (80) to target security group
- HTTPS (443) to target security group
```

## 📊 Monitoring and Logging

### CloudWatch Metrics
- **RequestCount**: Number of requests
- **TargetResponseTime**: Response time from targets
- **HTTPCode_Target_2XX_Count**: Successful responses
- **HTTPCode_ELB_5XX_Count**: Load balancer errors
- **HealthyHostCount**: Number of healthy targets

### Access Logs
- **S3 Storage**: Store access logs in S3
- **Log Format**: Detailed request information
- **Analysis**: Use for troubleshooting and analytics

### Access Log Fields
```
timestamp elb client:port target:port request_processing_time target_processing_time response_processing_time elb_status_code target_status_code received_bytes sent_bytes request verb url protocol user_agent ssl_cipher ssl_protocol
```

## 💰 Pricing and Cost Optimization

### ALB Pricing
- **Load Balancer Hours**: $0.0225 per hour
- **Load Balancer Capacity Units (LCU)**: $0.008 per LCU-hour
- **Data Processing**: Included in LCU pricing

### LCU Dimensions
- **New Connections**: 25 per second
- **Active Connections**: 3,000 per minute
- **Bandwidth**: 1 GB per hour
- **Rule Evaluations**: 1,000 per second

### Cost Optimization
- Right-size target groups
- Use efficient health check settings
- Optimize SSL/TLS configuration
- Monitor and optimize rule complexity

## 🎯 Best Practices

### Design
- Use multiple AZs for high availability
- Implement proper health checks
- Use appropriate target group settings
- Plan routing rules carefully

### Security
- Use HTTPS listeners with strong SSL policies
- Implement proper security group rules
- Enable access logging for monitoring
- Use WAF for additional protection

### Performance
- Optimize health check settings
- Use connection draining for deployments
- Monitor target response times
- Implement proper caching strategies

### Operations
- Monitor CloudWatch metrics
- Set up alarms for critical metrics
- Regular review of routing rules
- Implement proper logging and monitoring