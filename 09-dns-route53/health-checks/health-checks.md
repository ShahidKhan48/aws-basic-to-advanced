# Route 53 Health Checks

## 🏥 Health Checks Overview

### What are Health Checks?
- **Definition**: Monitoring service that checks resource health and availability
- **Purpose**: Enable automatic failover and traffic routing decisions
- **Types**: HTTP/HTTPS, TCP, Calculated, CloudWatch Alarm
- **Global**: Health checkers distributed worldwide

## 🌐 HTTP/HTTPS Health Checks

### HTTP Health Check Features
- **Protocol**: HTTP or HTTPS
- **Port**: Configurable port (default 80/443)
- **Path**: Specific URL path to check
- **String Matching**: Optional response body string matching
- **Status Codes**: Success based on HTTP status codes

### HTTP Health Check Configuration
```json
{
  "Type": "HTTP",
  "ResourcePath": "/health",
  "FullyQualifiedDomainName": "example.com",
  "Port": 80,
  "RequestInterval": 30,
  "FailureThreshold": 3,
  "SearchString": "OK"
}
```

### HTTPS Health Check Configuration
```json
{
  "Type": "HTTPS",
  "ResourcePath": "/api/health",
  "FullyQualifiedDomainName": "api.example.com",
  "Port": 443,
  "RequestInterval": 30,
  "FailureThreshold": 3,
  "EnableSNI": true
}
```

## 🔌 TCP Health Checks

### TCP Health Check Features
- **Protocol**: TCP connection test
- **Port**: Any TCP port
- **Connection**: Tests if port is reachable
- **Use Cases**: Non-HTTP services, databases, custom applications

### TCP Health Check Configuration
```json
{
  "Type": "TCP",
  "FullyQualifiedDomainName": "database.example.com",
  "Port": 3306,
  "RequestInterval": 30,
  "FailureThreshold": 3
}
```

### TCP Use Cases
- **Database Servers**: MySQL (3306), PostgreSQL (5432)
- **Mail Servers**: SMTP (25), IMAP (143)
- **Custom Services**: Any TCP-based service
- **Load Balancers**: TCP load balancer health

## 📊 Calculated Health Checks

### What are Calculated Health Checks?
- **Definition**: Combine multiple health checks using boolean logic
- **Operations**: AND, OR, NOT operations
- **Use Cases**: Complex health check scenarios
- **Hierarchy**: Create health check hierarchies

### Calculated Health Check Configuration
```json
{
  "Type": "CALCULATED",
  "ChildHealthChecks": [
    "health-check-1",
    "health-check-2",
    "health-check-3"
  ],
  "HealthThreshold": 2,
  "CloudWatchAlarmRegion": "us-east-1",
  "InsufficientDataHealthStatus": "Failure"
}
```

### Calculated Health Check Examples
```
Web Application Health:
├── Web Server Health (HTTP)
├── Database Health (TCP)
└── Cache Health (TCP)
Result: Healthy if 2 out of 3 are healthy

Multi-Region Application:
├── US-East Region Health
├── US-West Region Health
└── EU-West Region Health
Result: Healthy if any region is healthy
```

## ☁️ CloudWatch Alarm Health Checks

### What are CloudWatch Alarm Health Checks?
- **Definition**: Health checks based on CloudWatch alarms
- **Integration**: Monitor any CloudWatch metric
- **Use Cases**: Application metrics, custom metrics, AWS service metrics
- **Flexibility**: Most flexible health check type

### CloudWatch Alarm Health Check Configuration
```json
{
  "Type": "CLOUDWATCH_METRIC",
  "AlarmIdentifier": {
    "Region": "us-east-1",
    "Name": "HighCPUUtilization"
  },
  "InsufficientDataHealthStatus": "Failure"
}
```

### CloudWatch Alarm Use Cases
- **Application Metrics**: Response time, error rate
- **Infrastructure Metrics**: CPU, memory, disk usage
- **Custom Metrics**: Business-specific metrics
- **AWS Service Metrics**: RDS, ELB, Lambda metrics

## ⚙️ Health Check Configuration

### Request Interval
- **Standard**: 30 seconds (default)
- **Fast**: 10 seconds (additional cost)
- **Considerations**: Balance between detection speed and cost

### Failure Threshold
- **Range**: 1 to 10 consecutive failures
- **Default**: 3 failures
- **Considerations**: Balance between false positives and detection speed

### Health Check Regions
- **Global**: Health checkers in multiple regions
- **Consensus**: Majority of health checkers must agree
- **Regions**: US, Europe, Asia Pacific, South America

## 📈 Health Check Monitoring

### CloudWatch Metrics
- **HealthCheckStatus**: 1 (healthy) or 0 (unhealthy)
- **HealthCheckPercentHealthy**: Percentage of healthy checkers
- **ConnectionTime**: Time to establish connection
- **TimeToFirstByte**: Time to receive first byte

### SNS Notifications
- **Health Check Alarms**: Notify when health status changes
- **Configuration**: SNS topic for notifications
- **Use Cases**: Incident response, automated remediation

### Health Check Alarm Configuration
```json
{
  "AlarmName": "WebsiteHealthCheck",
  "MetricName": "HealthCheckStatus",
  "Namespace": "AWS/Route53",
  "Statistic": "Minimum",
  "Period": 60,
  "EvaluationPeriods": 2,
  "Threshold": 1,
  "ComparisonOperator": "LessThanThreshold",
  "AlarmActions": [
    "arn:aws:sns:us-east-1:123456789012:health-alerts"
  ]
}
```

## 🔧 Advanced Health Check Features

### String Matching
- **Purpose**: Verify response content
- **String**: Case-sensitive string search
- **Use Cases**: Verify application functionality
- **Limitation**: First 5120 bytes of response

### Request Headers
- **Custom Headers**: Add custom HTTP headers
- **Use Cases**: Authentication, routing, debugging
- **Format**: Header name and value pairs

### Health Check Tags
- **Organization**: Tag health checks for organization
- **Billing**: Cost allocation and tracking
- **Automation**: Automated management based on tags

## 💰 Health Check Pricing

### Pricing Components
- **Basic Health Checks**: $0.50 per health check per month
- **Fast Interval**: Additional $1.00 per health check per month
- **String Matching**: No additional cost
- **Calculated Health Checks**: $1.00 per health check per month

### Cost Optimization
- **Appropriate Intervals**: Use 30-second interval unless fast detection needed
- **Consolidate Checks**: Use calculated health checks to reduce total count
- **Monitor Usage**: Regular review of health check necessity
- **Cleanup**: Remove unused health checks

## 🎯 Best Practices

### Health Check Design
- **Meaningful Endpoints**: Create dedicated health check endpoints
- **Lightweight**: Keep health checks simple and fast
- **Dependencies**: Check critical dependencies
- **Avoid Side Effects**: Health checks shouldn't modify data

### Endpoint Implementation
```python
# Example health check endpoint
@app.route('/health')
def health_check():
    try:
        # Check database connection
        db.execute('SELECT 1')
        
        # Check external dependencies
        response = requests.get('https://api.dependency.com/health', timeout=5)
        
        if response.status_code == 200:
            return {'status': 'healthy', 'timestamp': datetime.utcnow()}, 200
        else:
            return {'status': 'unhealthy', 'reason': 'dependency_failed'}, 503
    except Exception as e:
        return {'status': 'unhealthy', 'error': str(e)}, 503
```

### Monitoring Strategy
- **Multiple Layers**: Health checks at different levels
- **Alerting**: Set up appropriate alerts
- **Documentation**: Document health check logic
- **Testing**: Regular testing of health check functionality

### Failover Configuration
- **Low TTL**: Use low TTL for DNS records with health checks
- **Multiple Regions**: Distribute resources across regions
- **Automated Recovery**: Implement automated recovery procedures
- **Manual Override**: Ability to manually override health checks