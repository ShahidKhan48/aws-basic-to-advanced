# S3 Bucket, Object & Key

## 🪣 S3 Buckets

### What is an S3 Bucket?
- **Definition**: Container for objects stored in Amazon S3
- **Global Namespace**: Bucket names must be globally unique
- **Regional**: Buckets are created in specific AWS regions
- **Unlimited Storage**: No limit on number of objects or total storage

### Bucket Naming Rules
- 3-63 characters long
- Lowercase letters, numbers, hyphens only
- Must start and end with letter or number
- No IP address format (e.g., 192.168.1.1)
- No uppercase letters or underscores

### Bucket Examples
```
✅ Valid Names:
- my-company-logs
- user-uploads-2024
- backup-data-prod

❌ Invalid Names:
- MyBucket (uppercase)
- my_bucket (underscore)
- 192.168.1.1 (IP format)
```

## 📄 S3 Objects

### What are S3 Objects?
- **Definition**: Files stored in S3 buckets
- **Size Limit**: 0 bytes to 5 TB per object
- **Components**:
  - Object data (the file content)
  - Metadata (key-value pairs)
  - Object key (unique identifier)

### Object Metadata
- **System Metadata**: Managed by S3 (size, last modified, etc.)
- **User Metadata**: Custom key-value pairs
- **HTTP Headers**: Content-Type, Cache-Control, etc.

### Object Versions
- **Versioning**: Multiple versions of same object
- **Version ID**: Unique identifier for each version
- **Current Version**: Latest version of object
- **Previous Versions**: Older versions retained

## 🔑 S3 Keys

### What is an S3 Key?
- **Definition**: Unique identifier for object within bucket
- **Format**: String that identifies object (like file path)
- **Case Sensitive**: Keys are case-sensitive
- **Length**: Up to 1,024 characters

### Key Structure Examples
```
Simple Keys:
- document.pdf
- image.jpg
- data.csv

Hierarchical Keys (folder-like):
- logs/2024/01/app.log
- images/thumbnails/photo1.jpg
- backups/database/2024-01-15.sql
```

### Key Best Practices
- Use meaningful, descriptive names
- Include date/time for time-series data
- Use prefixes for organization
- Avoid sequential naming for high request rates
- Consider using random prefixes for performance

## 🏗️ S3 URL Structure
```
https://bucket-name.s3.region.amazonaws.com/key-name
https://s3.region.amazonaws.com/bucket-name/key-name

Examples:
https://my-bucket.s3.us-east-1.amazonaws.com/documents/report.pdf
https://s3.us-west-2.amazonaws.com/my-bucket/images/logo.png
```

## 📊 S3 Storage Metrics
- **Objects**: Unlimited number per bucket
- **Size**: 5 TB maximum per object
- **Buckets**: 100 buckets per account (soft limit)
- **Requests**: Unlimited requests per second
- **Durability**: 99.999999999% (11 9's)
- **Availability**: 99.99% to 99.9% depending on storage class