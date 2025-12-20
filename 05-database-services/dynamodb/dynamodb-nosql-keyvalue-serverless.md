# DynamoDB - NoSQL, Key-Value & Serverless

## 🗃️ DynamoDB Overview

### What is DynamoDB?
- **Definition**: Fully managed NoSQL database service
- **Performance**: Single-digit millisecond latency
- **Scalability**: Seamless scaling to handle any workload
- **Serverless**: No servers to manage, pay-per-use

## 🔑 Key-Value Store

### Data Model
- **Tables**: Collection of items (no fixed schema)
- **Items**: Collection of attributes (like rows)
- **Attributes**: Name-value pairs (like columns)
- **Primary Key**: Uniquely identifies each item

### Primary Key Types
- **Partition Key**: Single attribute (simple primary key)
- **Composite Key**: Partition key + Sort key
- **Hash Function**: Distributes items across partitions

### Example Data Structure
```json
{
  "UserId": "user123",           // Partition Key
  "Timestamp": "2024-01-15",     // Sort Key
  "UserName": "john_doe",
  "Email": "john@example.com",
  "LoginCount": 42,
  "Preferences": {
    "Theme": "dark",
    "Language": "en"
  }
}
```

## 📄 Document Database

### Document Features
- **Nested Attributes**: Support for complex data structures
- **Data Types**: String, Number, Binary, Boolean, Null, List, Map, Set
- **JSON Support**: Native JSON document storage
- **Flexible Schema**: No predefined schema required

### Supported Data Types
- **Scalar**: String (S), Number (N), Binary (B), Boolean (BOOL), Null (NULL)
- **Document**: List (L), Map (M)
- **Set**: String Set (SS), Number Set (NS), Binary Set (BS)

## ⚡ Serverless Features

### On-Demand Mode
- **Capacity**: Automatically scales based on traffic
- **Pricing**: Pay per request (read/write)
- **No Planning**: No capacity planning required
- **Use Cases**: Unpredictable workloads, new applications

### Provisioned Mode
- **Capacity**: Specify read/write capacity units
- **Auto Scaling**: Automatically adjust capacity
- **Reserved Capacity**: Discounts for predictable workloads
- **Use Cases**: Predictable workloads, cost optimization

## 🔍 Query Patterns

### Primary Key Access
- **GetItem**: Retrieve single item by primary key
- **PutItem**: Create or replace item
- **UpdateItem**: Modify existing item
- **DeleteItem**: Remove item

### Query Operations
- **Query**: Find items with same partition key
- **Scan**: Examine all items in table (expensive)
- **Filter Expressions**: Filter results after query/scan
- **Projection Expressions**: Return specific attributes only

### Secondary Indexes
- **Global Secondary Index (GSI)**: Different partition and sort key
- **Local Secondary Index (LSI)**: Same partition key, different sort key
- **Use Cases**: Support different query patterns

## 🚀 Performance Features

### DynamoDB Accelerator (DAX)
- **Purpose**: In-memory caching for DynamoDB
- **Latency**: Microsecond latency for cached data
- **Compatibility**: Drop-in replacement for DynamoDB API
- **Use Cases**: Read-heavy applications, gaming, real-time bidding

### Global Tables
- **Multi-Region**: Replicate tables across regions
- **Active-Active**: Read and write in any region
- **Consistency**: Eventually consistent across regions
- **Use Cases**: Global applications, disaster recovery

## 📊 Consistency Models

### Eventually Consistent Reads (Default)
- **Performance**: Higher throughput, lower latency
- **Consistency**: May not reflect recent writes
- **Cost**: Lower cost (1 RCU per 4KB)

### Strongly Consistent Reads
- **Consistency**: Always returns most recent data
- **Performance**: Higher latency
- **Cost**: Higher cost (1 RCU per 4KB, but uses 2x capacity)

## 🔄 Streams and Triggers

### DynamoDB Streams
- **Purpose**: Capture changes to table items
- **Retention**: 24 hours
- **Records**: INSERT, MODIFY, REMOVE events
- **Integration**: Lambda, Kinesis, analytics

### Lambda Triggers
- **Event-Driven**: Automatically invoke Lambda functions
- **Use Cases**: Real-time processing, notifications, analytics
- **Batch Size**: Process multiple records per invocation

## 💰 Pricing Models

### On-Demand Pricing
- **Read Requests**: $0.25 per million
- **Write Requests**: $1.25 per million
- **Storage**: $0.25 per GB-month
- **Best For**: Unpredictable traffic patterns

### Provisioned Pricing
- **Read Capacity Units (RCU)**: $0.09 per RCU-month
- **Write Capacity Units (WCU)**: $0.47 per WCU-month
- **Storage**: $0.25 per GB-month
- **Best For**: Predictable traffic patterns

## 🎯 Best Practices

### Design Patterns
- Design for uniform data distribution
- Use composite keys for hierarchical data
- Implement single-table design when possible
- Use GSIs for different access patterns

### Performance Optimization
- Avoid hot partitions
- Use batch operations for multiple items
- Implement exponential backoff for retries
- Monitor throttling and consumed capacity

### Cost Optimization
- Choose appropriate capacity mode
- Use on-demand for unpredictable workloads
- Monitor and optimize unused indexes
- Implement data lifecycle management

### Security
- Use IAM for access control
- Enable encryption at rest and in transit
- Implement fine-grained access control
- Use VPC endpoints for private access