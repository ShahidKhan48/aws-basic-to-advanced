# Region, Availability Zone & Edge Location

## 🌍 AWS Regions
- **Definition**: Geographic areas containing multiple data centers
- **Current Count**: 30+ regions worldwide
- **Selection Criteria**:
  - Latency to users
  - Data sovereignty requirements
  - Service availability
  - Cost considerations

### Popular Regions
- **us-east-1** (N. Virginia): Largest, most services
- **us-west-2** (Oregon): Popular for production
- **eu-west-1** (Ireland): European operations
- **ap-southeast-1** (Singapore): Asia Pacific

## 🏢 Availability Zones (AZs)
- **Definition**: Isolated data centers within a region
- **Characteristics**:
  - Physically separated (miles apart)
  - Connected via high-speed, low-latency links
  - Independent power, cooling, networking
  - Minimum 3 AZs per region

### AZ Naming
- Format: `region-code` + `letter` (e.g., us-east-1a, us-east-1b)
- Letters are randomized per account for load distribution

## 🚀 Edge Locations
- **Purpose**: Content delivery and caching
- **Count**: 400+ edge locations globally
- **Services**:
  - CloudFront CDN
  - Route 53 DNS
  - AWS Global Accelerator

### Benefits
- Reduced latency for end users
- Improved content delivery performance
- Global reach without infrastructure investment

## Best Practices
- Deploy across multiple AZs for high availability
- Choose regions closest to your users
- Consider compliance requirements
- Use edge locations for content delivery