# EBS (Elastic Block Store)

## 💾 What is EBS?
- **Definition**: Persistent block storage for EC2 instances
- **Characteristics**:
  - Network-attached storage
  - Persistent (survives instance termination)
  - Highly available and durable
  - Scalable performance and capacity

## 🔧 EBS Volume Types

### General Purpose SSD (gp3/gp2)
- **gp3**: Latest generation, customizable IOPS and throughput
- **gp2**: Previous generation, burstable IOPS
- **Use Cases**: Boot volumes, development, testing, low-latency apps
- **Performance**: Up to 16,000 IOPS, 1,000 MB/s throughput

### Provisioned IOPS SSD (io2/io1)
- **io2**: Latest generation, higher durability
- **io1**: Previous generation
- **Use Cases**: Critical business applications, databases
- **Performance**: Up to 64,000 IOPS, 1,000 MB/s throughput
- **Features**: Multi-Attach support (io1/io2 only)

### Throughput Optimized HDD (st1)
- **Use Cases**: Big data, data warehouses, log processing
- **Performance**: Up to 500 IOPS, 500 MB/s throughput
- **Cost**: Lower cost per GB than SSD
- **Cannot**: Be used as boot volume

### Cold HDD (sc1)
- **Use Cases**: Infrequent access, lowest cost storage
- **Performance**: Up to 250 IOPS, 250 MB/s throughput
- **Cost**: Lowest cost per GB
- **Cannot**: Be used as boot volume

## 📊 EBS Features

### Snapshots
- **Purpose**: Point-in-time backup of EBS volumes
- **Storage**: Stored in Amazon S3
- **Incremental**: Only changed blocks since last snapshot
- **Cross-Region**: Can copy snapshots across regions
- **Automation**: Use Data Lifecycle Manager for automated snapshots

### Encryption
- **At Rest**: Data encrypted on volume
- **In Transit**: Data encrypted between instance and volume
- **Key Management**: AWS KMS integration
- **Performance**: Minimal impact on performance

### Multi-Attach (io1/io2 only)
- **Feature**: Attach single volume to multiple instances
- **Use Cases**: Cluster file systems, shared storage
- **Limitations**: Same AZ only, specific instance types

## 🎯 Best Practices
- Choose appropriate volume type for workload
- Enable encryption for sensitive data
- Regular snapshots for backup
- Monitor performance metrics
- Right-size volumes for cost optimization
- Use gp3 for most workloads (better price/performance)