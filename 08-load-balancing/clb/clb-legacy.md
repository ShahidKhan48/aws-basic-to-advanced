# CLB - Classic Load Balancer (Legacy)

## 📜 Classic Load Balancer Overview

### What is CLB?
- **Definition**: Previous generation load balancer (Legacy)
- **Protocols**: HTTP, HTTPS, TCP, SSL
- **Status**: Legacy service, not recommended for new deployments
- **Replacement**: ALB for Layer 7, NLB for Layer 4

## ⚠️ Legacy Status

### Why Legacy?
- **Limited Features**: Fewer features compared to ALB/NLB
- **No Advanced Routing**: Basic load balancing only
- **Performance**: Lower performance than modern load balancers
- **Cost**: Less cost-effective than ALB/NLB

### Migration Path
- **HTTP/HTTPS Traffic**: Migrate to ALB
- **TCP/UDP Traffic**: Migrate to NLB
- **Mixed Traffic**: Use multiple load balancers
- **Timeline**: Plan migration to modern load balancers

## 🔧 CLB Features

### Basic Load Balancing
- **Round Robin**: Default algorithm
- **Sticky Sessions**: Session affinity support
- **Health Checks**: Basic health monitoring
- **SSL Termination**: SSL/TLS support

### Supported Protocols
- **HTTP**: Port 80 traffic
- **HTTPS**: Port 443 with SSL termination
- **TCP**: Layer 4 load balancing
- **SSL**: Secure TCP connections

## 🏥 Health Checks

### Health Check Configuration
```
Protocol: HTTP
Port: 80
Path: /
Interval: 30 seconds
Timeout: 5 seconds
Healthy Threshold: 10
Unhealthy Threshold: 2
```

### Health Check Limitations
- **Simple Checks**: Basic HTTP/TCP checks only
- **Limited Customization**: Fewer configuration options
- **No Advanced Logic**: No complex health check rules

## 🔒 SSL/TLS Support

### SSL Termination
- **Certificate Management**: Upload certificates manually
- **No ACM Integration**: Limited certificate management
- **SSL Policies**: Basic SSL policy support
- **Performance**: Less optimized than ALB/NLB

### SSL Configuration
```
SSL Certificate: Upload to IAM or use ACM
SSL Policy: ELBSecurityPolicy-2016-08
Cipher Suites: Limited selection
Protocol Support: TLS 1.0, 1.1, 1.2
```

## 📊 Monitoring

### CloudWatch Metrics
- **RequestCount**: Number of requests
- **Latency**: Response time
- **HTTPCode_ELB_4XX**: Client errors
- **HTTPCode_ELB_5XX**: Server errors
- **HealthyHostCount**: Healthy instances

### Limited Monitoring
- **Basic Metrics**: Fewer metrics than ALB/NLB
- **No Advanced Insights**: Limited performance visibility
- **Simple Logging**: Basic access log format

## 💰 Pricing

### CLB Pricing
- **Load Balancer Hours**: $0.025 per hour
- **Data Transfer**: $0.008 per GB processed
- **Simple Pricing**: No LCU-based pricing

### Cost Comparison
- **Generally Higher**: More expensive than ALB/NLB
- **Less Efficient**: Lower performance per dollar
- **No Optimization**: Limited cost optimization features

## 🚫 Limitations

### Feature Limitations
- **No Path-Based Routing**: Cannot route based on URL path
- **No Host-Based Routing**: Cannot route based on host header
- **No WebSocket**: Limited protocol support
- **No HTTP/2**: No modern protocol support

### Scalability Limitations
- **Pre-warming Required**: Manual scaling preparation
- **Lower Performance**: Compared to modern load balancers
- **Connection Limits**: Lower concurrent connection limits

### Management Limitations
- **Basic Configuration**: Limited configuration options
- **No Target Groups**: Direct instance registration only
- **Manual Management**: More manual intervention required

## 🔄 Migration Strategies

### Migrate to ALB
```
Use Cases:
- HTTP/HTTPS applications
- Need advanced routing
- Microservices architecture
- Container applications

Benefits:
- Path-based routing
- Host-based routing
- Better performance
- Lower cost
```

### Migrate to NLB
```
Use Cases:
- TCP/UDP applications
- High performance requirements
- Static IP requirements
- Low latency needs

Benefits:
- Ultra-high performance
- Static IP addresses
- Lower latency
- Better scaling
```

## 📋 Migration Checklist

### Pre-Migration Assessment
- [ ] Identify traffic patterns
- [ ] Document current configuration
- [ ] Plan target load balancer type
- [ ] Test migration in non-production

### Migration Steps
1. **Create New Load Balancer**: ALB or NLB
2. **Configure Target Groups**: Set up targets
3. **Update DNS**: Point to new load balancer
4. **Monitor Traffic**: Ensure proper operation
5. **Decommission CLB**: Remove old load balancer

### Post-Migration Validation
- [ ] Verify all traffic routing correctly
- [ ] Check health check status
- [ ] Monitor performance metrics
- [ ] Update monitoring and alerting

## 🎯 Recommendations

### For New Deployments
- **Don't Use CLB**: Choose ALB or NLB instead
- **ALB for HTTP/HTTPS**: Web applications, APIs
- **NLB for TCP/UDP**: High-performance applications
- **Consider Requirements**: Choose based on specific needs

### For Existing CLB Users
- **Plan Migration**: Develop migration timeline
- **Start with Non-Critical**: Migrate test environments first
- **Monitor Performance**: Compare before and after
- **Update Documentation**: Reflect new architecture

### Best Practices
- **Avoid New CLB**: Use modern load balancers
- **Migrate Gradually**: Phased migration approach
- **Test Thoroughly**: Validate functionality
- **Monitor Closely**: Watch for issues during migration

## 📚 Learning Resources

### AWS Documentation
- CLB User Guide (for reference)
- ALB Migration Guide
- NLB Migration Guide
- Load Balancer Comparison

### Migration Tools
- AWS Load Balancer Migration Tool
- CloudFormation templates
- Terraform modules
- AWS CLI scripts