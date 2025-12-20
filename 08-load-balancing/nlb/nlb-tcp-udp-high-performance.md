# NLB - TCP/UDP & High Performance

## ⚡ Network Load Balancer (NLB) Overview

### What is NLB?
- **Definition**: Layer 4 (Transport) load balancer
- **Protocols**: TCP, UDP, TLS
- **Purpose**: Ultra-high performance, low latency load balancing
- **Capacity**: Millions of requests per second

## 🚀 High Performance Features

### Performance Characteristics
- **Latency**: Ultra-low latency (microseconds)
- **Throughput**: Millions of requests per second
- **Connections**: Millions of concurrent connections
- **Scaling**: Automatic scaling without pre-warming

### Architecture Benefits
- **Static IP**: Each AZ gets a static IP address
- **Elastic IP**: Can assign Elastic IP to each AZ
- **Direct Server Return**: Optional for even lower latency
- **Connection Multiplexing**: Efficient connection handling

## 🌐 Protocol Support

### TCP Load Balancing
- **Use Cases**: Databases, gaming, IoT applications
- **Features**: Connection-based load balancing
- **Health Checks**: TCP or HTTP/HTTPS health checks
- **Persistence**: Source IP-based session affinity

### UDP Load Balancing
- **Use Cases**: DNS, syslog, gaming protocols
- **Features**: Flow-based load balancing
- **Health Checks**: TCP or HTTP/HTTPS health checks
- **Flow Hash**: 5-tuple flow hash algorithm

### TLS Load Balancing
- **Use Cases**: Secure TCP applications
- **Features**: TLS termination at load balancer
- **Certificates**: ACM integration
- **Performance**: Hardware-accelerated TLS processing

## 🎯 Target Types

### EC2 Instances
- **Registration**: By instance ID
- **Health Checks**: Instance-level health monitoring
- **Port Mapping**: Flexible port configuration
- **Cross-Zone**: Optional cross-zone load balancing

### IP Addresses
- **Registration**: By IP address and port
- **Use Cases**: On-premises servers, containers
- **Network**: Must be within VPC CIDR or connected networks
- **Flexibility**: Target any IP within network

### Application Load Balancers
- **Chaining**: NLB in front of ALB
- **Use Cases**: Static IP requirement with Layer 7 features
- **Benefits**: Combine NLB performance with ALB intelligence
- **Architecture**: NLB → ALB → Targets

## 🔧 NLB Configuration

### Listener Configuration
```json
{
  "Protocol": "TCP",
  "Port": 80,
  "DefaultActions": [
    {
      "Type": "forward",
      "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/tcp-targets/1234567890123456"
    }
  ]
}
```

### Target Group Settings
```json
{
  "Protocol": "TCP",
  "Port": 80,
  "VpcId": "vpc-12345678",
  "HealthCheckProtocol": "HTTP",
  "HealthCheckPath": "/health",
  "HealthCheckIntervalSeconds": 30,
  "HealthyThresholdCount": 3,
  "UnhealthyThresholdCount": 3
}
```

## 🏥 Health Checks

### Health Check Types
- **TCP**: Simple TCP connection test
- **HTTP/HTTPS**: HTTP-based health checks
- **Custom**: Application-specific health checks

### Health Check Configuration
```
Protocol: HTTP
Port: 80
Path: /health
Interval: 30 seconds
Timeout: 6 seconds
Healthy Threshold: 3
Unhealthy Threshold: 3
```

### Health Check Best Practices
- Use HTTP health checks for better visibility
- Implement meaningful health check endpoints
- Set appropriate timeout and threshold values
- Monitor health check metrics

## 🌍 Cross-Zone Load Balancing

### Default Behavior
- **Disabled by Default**: Traffic distributed within AZ only
- **Cost**: No additional charges when disabled
- **Availability**: Higher availability when enabled

### When to Enable
- **Uneven Target Distribution**: Different number of targets per AZ
- **High Availability**: Better fault tolerance
- **Traffic Distribution**: Even distribution across all targets

### Cost Considerations
- **Data Transfer Charges**: Cross-AZ data transfer costs apply
- **Performance**: Slight increase in latency
- **Trade-off**: Cost vs. availability and distribution

## 📊 Monitoring and Metrics

### CloudWatch Metrics
- **ActiveFlowCount**: Number of active flows
- **NewFlowCount**: Number of new flows
- **ProcessedBytes**: Bytes processed by load balancer
- **TargetTLSNegotiationErrorCount**: TLS negotiation errors
- **HealthyHostCount**: Number of healthy targets

### Connection Tracking
- **Flow Tracking**: 5-tuple flow identification
- **Connection State**: Track connection lifecycle
- **Idle Timeout**: Configurable idle timeout (30-4000 seconds)

## 🔒 Security Features

### Security Groups
```
Inbound Rules:
- TCP (80) from 0.0.0.0/0
- TCP (443) from 0.0.0.0/0
- UDP (53) from 0.0.0.0/0

Outbound Rules:
- All traffic to target security groups
```

### TLS Configuration
- **Certificate Management**: ACM integration
- **TLS Policies**: Configurable TLS policies
- **SNI Support**: Server Name Indication
- **Perfect Forward Secrecy**: Supported cipher suites

## 💡 Use Cases

### High-Performance Applications
- **Gaming**: Low-latency gaming servers
- **Financial**: High-frequency trading systems
- **IoT**: Large-scale IoT device communication
- **Streaming**: Real-time media streaming

### Static IP Requirements
- **Firewall Rules**: Whitelist specific IP addresses
- **DNS**: Simple DNS configuration
- **Third-party Integration**: External systems requiring static IPs
- **Compliance**: Regulatory requirements for static IPs

### Hybrid Architectures
- **On-premises Integration**: Load balance to on-premises servers
- **Multi-cloud**: Distribute traffic across cloud providers
- **Migration**: Gradual migration scenarios
- **Disaster Recovery**: Failover to different environments

## 💰 Pricing

### NLB Pricing Components
- **Load Balancer Hours**: $0.0225 per hour
- **Network Load Balancer Capacity Units (NLCU)**: $0.006 per NLCU-hour
- **Data Processing**: Included in NLCU pricing

### NLCU Dimensions
- **New Connections**: 800 per second
- **Active Connections**: 100,000 per minute
- **Bandwidth**: 1 GB per hour

## 🎯 Best Practices

### Design
- Deploy across multiple AZs
- Use appropriate target types
- Configure proper health checks
- Plan for traffic patterns

### Performance
- Use TCP for maximum performance
- Configure appropriate idle timeouts
- Monitor connection metrics
- Optimize target distribution

### Security
- Implement proper security group rules
- Use TLS for encrypted traffic
- Monitor security metrics
- Regular security assessments

### Cost Optimization
- Monitor NLCU usage
- Optimize cross-zone load balancing
- Right-size target groups
- Use Reserved Capacity when applicable