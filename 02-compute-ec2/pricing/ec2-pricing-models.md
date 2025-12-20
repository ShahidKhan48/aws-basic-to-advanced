# EC2 Pricing Models

## ⚡ On-Demand Instances
- **Definition**: Pay for compute capacity by hour/second
- **Billing**: No upfront costs, no long-term commitments
- **Characteristics**:
  - Highest cost per hour
  - Available immediately
  - Can start/stop anytime
- **Use Cases**:
  - Short-term, irregular workloads
  - Development and testing
  - Applications with unpredictable usage
  - First-time applications on AWS

## 📅 Reserved Instances (RIs)
- **Definition**: Commit to usage for 1-3 years for significant discounts
- **Discount**: Up to 75% compared to On-Demand
- **Terms**: 1 year or 3 years
- **Payment Options**:
  - **All Upfront**: Highest discount, pay entire amount upfront
  - **Partial Upfront**: Moderate discount, partial upfront + monthly
  - **No Upfront**: Lowest discount, monthly payments only

### RI Types
- **Standard RIs**: 
  - Highest discount (up to 75%)
  - Fixed instance family, size, platform, tenancy
  - Can modify AZ within same region
- **Convertible RIs**:
  - Lower discount (up to 54%)
  - Can change instance family, OS, tenancy
  - More flexibility for changing requirements

### RI Marketplace
- Sell unused Standard RIs
- Buy RIs from other AWS customers
- Shorter terms available (less than 3 years remaining)

## 🎯 Spot Instances
- **Definition**: Bid on unused EC2 capacity
- **Discount**: Up to 90% compared to On-Demand
- **Interruption**: Can be terminated with 2-minute notice
- **Pricing**: Fluctuates based on supply and demand

### Spot Instance Features
- **Spot Fleet**: Launch mix of instance types across AZs
- **Spot Blocks**: Run for 1-6 hours without interruption
- **Hibernation**: Save instance state to EBS when interrupted

### Best Practices for Spot
- Use for fault-tolerant applications
- Implement graceful shutdown handling
- Diversify across instance types and AZs
- Combine with On-Demand for critical components
- Use Spot Fleet for better availability

## 💰 Cost Optimization Strategies
- **Right-sizing**: Choose appropriate instance types
- **Reserved Instances**: For predictable workloads
- **Spot Instances**: For flexible, fault-tolerant workloads
- **Auto Scaling**: Scale based on demand
- **Scheduled Scaling**: For predictable patterns
- **Instance Scheduler**: Stop instances during off-hours