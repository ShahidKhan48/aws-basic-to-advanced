# AWS Pricing Models

## 💳 Pay-as-you-go
- **Concept**: Pay only for what you use
- **Benefits**:
  - No upfront costs
  - No long-term commitments
  - Scale up or down as needed
- **Best For**: Variable workloads, testing, development

## ⚡ On-Demand Pricing
- **Definition**: Standard pay-per-use pricing
- **Characteristics**:
  - Highest cost per hour/second
  - No commitments required
  - Available immediately
- **Use Cases**:
  - Short-term workloads
  - Unpredictable usage patterns
  - Development and testing
  - First-time applications

## 📅 Reserved Instances
- **Definition**: Commit to usage for 1-3 years for discounts
- **Discount**: Up to 75% compared to On-Demand
- **Types**:
  - **Standard**: Highest discount, fixed instance type
  - **Convertible**: Lower discount, can change instance family
  - **Scheduled**: For predictable recurring schedules
- **Payment Options**:
  - All Upfront: Highest discount
  - Partial Upfront: Moderate discount
  - No Upfront: Lowest discount
- **Best For**: Steady-state workloads, predictable usage

## 🎯 Spot Instances
- **Definition**: Bid on unused EC2 capacity
- **Discount**: Up to 90% compared to On-Demand
- **Characteristics**:
  - Can be interrupted with 2-minute notice
  - Price fluctuates based on supply/demand
- **Use Cases**:
  - Fault-tolerant applications
  - Batch processing
  - Big data analytics
  - CI/CD workloads
- **Best Practices**:
  - Use Spot Fleet for diversification
  - Implement graceful shutdown handling
  - Combine with On-Demand for critical components