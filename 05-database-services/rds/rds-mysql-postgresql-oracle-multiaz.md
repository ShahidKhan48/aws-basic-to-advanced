# RDS - MySQL, PostgreSQL, Oracle & Multi-AZ

## 🗄️ Amazon RDS Overview

### What is RDS?
- **Definition**: Managed relational database service
- **Purpose**: Simplify database administration tasks
- **Benefits**: Automated backups, patching, monitoring, scaling
- **Supported Engines**: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora

## 🐬 MySQL on RDS

### MySQL Features
- **Versions**: MySQL 5.7, 8.0
- **Storage**: General Purpose (gp2/gp3), Provisioned IOPS (io1/io2)
- **Instance Classes**: db.t3, db.m5, db.r5, db.x1e
- **Max Storage**: 64 TiB
- **Read Replicas**: Up to 15 read replicas

### MySQL Use Cases
- Web applications
- E-commerce platforms
- Content management systems
- Small to medium-scale applications

## 🐘 PostgreSQL on RDS

### PostgreSQL Features
- **Versions**: PostgreSQL 11, 12, 13, 14, 15
- **Advanced Features**: JSON support, full-text search, extensions
- **Storage**: Same as MySQL (gp2/gp3, io1/io2)
- **Instance Classes**: Same as MySQL
- **Read Replicas**: Up to 15 read replicas

### PostgreSQL Use Cases
- Complex queries and analytics
- Geospatial applications (PostGIS)
- JSON document storage
- Enterprise applications

## 🏛️ Oracle on RDS

### Oracle Features
- **Editions**: Standard Edition (SE2), Enterprise Edition (EE)
- **Versions**: Oracle 12c, 19c, 21c
- **Licensing**: License Included or Bring Your Own License (BYOL)
- **Storage**: Up to 64 TiB
- **Instance Classes**: Optimized for Oracle workloads

### Oracle Use Cases
- Enterprise applications
- ERP systems
- Legacy application migration
- Complex business logic requirements

## 🔄 Multi-AZ Deployments

### What is Multi-AZ?
- **Definition**: Synchronous replication to standby instance in different AZ
- **Purpose**: High availability and automatic failover
- **RTO**: Typically 1-2 minutes for failover
- **Data Loss**: Zero data loss (synchronous replication)

### Multi-AZ Features
- **Automatic Failover**: No manual intervention required
- **Backup**: Backups taken from standby (no performance impact)
- **Maintenance**: Performed on standby first
- **Monitoring**: CloudWatch metrics for both instances

### Multi-AZ vs Read Replicas
| Feature | Multi-AZ | Read Replicas |
|---------|----------|---------------|
| Purpose | High Availability | Read Scaling |
| Replication | Synchronous | Asynchronous |
| Failover | Automatic | Manual promotion |
| Endpoint | Same endpoint | Separate endpoints |
| Cost | ~2x primary cost | Additional instance cost |

## 🛠️ RDS Management Features

### Automated Backups
- **Retention**: 0-35 days (default 7 days)
- **Window**: Configurable backup window
- **Point-in-Time Recovery**: Restore to any second within retention period
- **Storage**: Stored in S3, no additional charge

### Manual Snapshots
- **Retention**: Until manually deleted
- **Cross-Region**: Can copy to other regions
- **Sharing**: Can share with other AWS accounts
- **Encryption**: Supports encryption at rest

### Maintenance Windows
- **Automatic**: Minor version updates, patches
- **Scheduled**: Major version upgrades (manual)
- **Window**: Configurable maintenance window
- **Multi-AZ**: Standby updated first

## 📊 Performance and Monitoring

### CloudWatch Metrics
- CPU Utilization
- Database Connections
- Read/Write IOPS
- Read/Write Latency
- Free Storage Space

### Performance Insights
- **Purpose**: Database performance monitoring
- **Features**: Top SQL statements, wait events, database load
- **Retention**: 7 days free, up to 2 years with additional cost

## 💰 Cost Optimization
- **Right-sizing**: Choose appropriate instance types
- **Reserved Instances**: 1-3 year commitments for discounts
- **Storage Optimization**: Use gp3 for better price/performance
- **Read Replicas**: Offload read traffic from primary
- **Scheduled Scaling**: Scale based on predictable patterns

## 🎯 Best Practices
- Enable Multi-AZ for production workloads
- Use read replicas for read-heavy applications
- Implement proper backup and retention policies
- Monitor performance metrics regularly
- Use parameter groups for configuration management
- Enable encryption for sensitive data