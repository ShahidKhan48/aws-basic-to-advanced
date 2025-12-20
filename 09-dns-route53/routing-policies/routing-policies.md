# Routing Policies - Simple, Weighted, Latency, Failover & Geo

## 🎯 Routing Policies Overview

### What are Routing Policies?
- **Definition**: Rules that determine how Route 53 responds to DNS queries
- **Purpose**: Control traffic distribution and routing decisions
- **Types**: Simple, Weighted, Latency, Failover, Geolocation, Geoproximity, Multivalue

## 📍 Simple Routing

### What is Simple Routing?
- **Definition**: Standard DNS resolution with no special routing logic
- **Use Case**: Single resource serving traffic for a domain
- **Limitation**: No health checks, no traffic distribution
- **Default**: Default routing policy

### Simple Routing Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.1"}
  ]
}
```

### Simple Routing with Multiple Values
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

## ⚖️ Weighted Routing

### What is Weighted Routing?
- **Definition**: Distribute traffic across multiple resources based on weights
- **Use Cases**: A/B testing, gradual deployments, load distribution
- **Weight Range**: 0 to 255
- **Calculation**: Weight / Total Weight = Traffic Percentage

### Weighted Routing Example
```
Record 1: Weight 70 (70% traffic)
Record 2: Weight 20 (20% traffic)
Record 3: Weight 10 (10% traffic)
Total Weight: 100
```

### Weighted Routing Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "Primary",
  "Weight": 70,
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.1"}
  ]
},
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "Secondary",
  "Weight": 30,
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.2"}
  ]
}
```

### Weighted Routing Use Cases
- **Blue/Green Deployments**: Gradual traffic shift
- **A/B Testing**: Split traffic for testing
- **Load Distribution**: Distribute load across regions
- **Canary Releases**: Small percentage to new version

## 🌍 Latency-Based Routing

### What is Latency-Based Routing?
- **Definition**: Route traffic to resource with lowest latency
- **Measurement**: AWS measures latency from user's location
- **Purpose**: Improve user experience with faster response times
- **Scope**: Global latency optimization

### Latency Routing Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "US-East",
  "Region": "us-east-1",
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.1"}
  ]
},
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "EU-West",
  "Region": "eu-west-1",
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.2"}
  ]
}
```

### Latency Routing Benefits
- **Performance**: Faster response times
- **Global Reach**: Serve users worldwide efficiently
- **Automatic**: AWS handles latency measurements
- **Dynamic**: Routing adjusts based on network conditions

## 🚨 Failover Routing

### What is Failover Routing?
- **Definition**: Route traffic to backup resource when primary fails
- **Health Checks**: Requires health checks to determine resource status
- **Types**: Active-Passive failover
- **Purpose**: High availability and disaster recovery

### Failover Routing Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "Primary",
  "Failover": "PRIMARY",
  "TTL": 60,
  "ResourceRecords": [
    {"Value": "203.0.113.1"}
  ],
  "HealthCheckId": "health-check-primary"
},
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "Secondary",
  "Failover": "SECONDARY",
  "TTL": 60,
  "ResourceRecords": [
    {"Value": "203.0.113.2"}
  ]
}
```

### Failover Routing Requirements
- **Health Checks**: Primary resource must have health check
- **Low TTL**: Use low TTL for faster failover
- **Monitoring**: Monitor health check status
- **Testing**: Regular failover testing

## 🗺️ Geolocation Routing

### What is Geolocation Routing?
- **Definition**: Route traffic based on user's geographic location
- **Granularity**: Continent, country, or US state level
- **Use Cases**: Content localization, compliance, licensing
- **Default**: Optional default location for unmatched queries

### Geolocation Routing Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "US-Users",
  "GeoLocation": {
    "CountryCode": "US"
  },
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.1"}
  ]
},
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "EU-Users",
  "GeoLocation": {
    "ContinentCode": "EU"
  },
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.2"}
  ]
}
```

### Geolocation Use Cases
- **Content Localization**: Serve region-specific content
- **Compliance**: Meet data residency requirements
- **Licensing**: Restrict content by geography
- **Performance**: Route to regional resources

## 📍 Geoproximity Routing

### What is Geoproximity Routing?
- **Definition**: Route traffic based on geographic location with bias adjustment
- **Bias**: Adjust routing to expand or shrink geographic regions
- **Traffic Flow**: Requires Route 53 Traffic Flow (additional cost)
- **Flexibility**: More control than geolocation routing

### Geoproximity Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "US-East-Biased",
  "GeoLocation": {
    "Coordinates": {
      "Latitude": "40.7128",
      "Longitude": "-74.0060"
    }
  },
  "Bias": 50,
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.1"}
  ]
}
```

## 🔢 Multivalue Answer Routing

### What is Multivalue Answer Routing?
- **Definition**: Return multiple healthy resources in response to DNS queries
- **Health Checks**: Each record can have associated health check
- **Client Selection**: Client chooses from returned values
- **Limit**: Up to 8 healthy records returned

### Multivalue Configuration
```json
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "Server-1",
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.1"}
  ],
  "HealthCheckId": "health-check-1"
},
{
  "Name": "www.example.com",
  "Type": "A",
  "SetIdentifier": "Server-2",
  "TTL": 300,
  "ResourceRecords": [
    {"Value": "203.0.113.2"}
  ],
  "HealthCheckId": "health-check-2"
}
```

## 🔄 Combining Routing Policies

### Nested Routing Policies
- **Primary Policy**: Top-level routing decision
- **Secondary Policy**: Applied within primary groups
- **Example**: Geolocation + Weighted routing

### Complex Routing Example
```
1. Geolocation Routing (by country)
   ├── US Traffic → Weighted Routing
   │   ├── 70% → US-East-1
   │   └── 30% → US-West-2
   └── EU Traffic → Latency Routing
       ├── EU-West-1
       └── EU-Central-1
```

## 🎯 Best Practices

### Policy Selection
- **Simple**: Single resource, no special requirements
- **Weighted**: A/B testing, gradual deployments
- **Latency**: Global applications, performance critical
- **Failover**: High availability requirements
- **Geolocation**: Compliance, content localization

### Health Checks
- **Always Use**: With failover routing
- **Consider**: For weighted and multivalue routing
- **Monitor**: Health check status and costs
- **Test**: Regular failover testing

### TTL Considerations
- **Failover**: Use low TTL (60 seconds)
- **Weighted**: Medium TTL (300 seconds)
- **Latency**: Standard TTL (300-3600 seconds)
- **Geolocation**: Higher TTL (3600+ seconds)

### Monitoring and Testing
- **CloudWatch**: Monitor DNS query patterns
- **Health Checks**: Monitor resource availability
- **Testing**: Regular testing of routing policies
- **Documentation**: Document routing logic and decisions