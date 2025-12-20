# S3 Versioning, Lifecycle, Encryption & Static Website

## 📚 S3 Versioning

### What is Versioning?
- **Definition**: Keep multiple versions of objects in same bucket
- **Purpose**: Protect against accidental deletion or modification
- **States**: Unversioned (default), Versioning-enabled, Versioning-suspended

### Versioning Features
- **Version ID**: Unique identifier for each object version
- **Current Version**: Latest version of object
- **Previous Versions**: Older versions retained
- **Delete Protection**: Delete markers instead of permanent deletion

### Versioning Best Practices
- Enable on critical buckets
- Use lifecycle policies to manage old versions
- Monitor storage costs (all versions consume space)
- Use MFA Delete for additional protection

## ♻️ Lifecycle Policies

### What are Lifecycle Policies?
- **Definition**: Rules to automatically transition or delete objects
- **Purpose**: Optimize costs by moving data to appropriate storage classes
- **Scope**: Apply to entire bucket or specific prefixes/tags

### Lifecycle Actions
- **Transition Actions**: Move to different storage class
- **Expiration Actions**: Delete objects after specified time
- **Version Actions**: Manage non-current versions
- **Multipart Upload**: Clean up incomplete uploads

### Example Lifecycle Policy
```json
{
  "Rules": [
    {
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ],
      "Expiration": {
        "Days": 2555
      }
    }
  ]
}
```

## 🔐 S3 Encryption

### Encryption Types
- **SSE-S3**: Server-side encryption with S3-managed keys
- **SSE-KMS**: Server-side encryption with KMS-managed keys
- **SSE-C**: Server-side encryption with customer-provided keys
- **CSE**: Client-side encryption

### SSE-S3 (Default)
- **Key Management**: AWS manages encryption keys
- **Algorithm**: AES-256
- **Cost**: No additional charges
- **Use Case**: Basic encryption needs

### SSE-KMS
- **Key Management**: AWS KMS manages keys
- **Features**: Key rotation, access logging, fine-grained permissions
- **Cost**: KMS key usage charges apply
- **Use Case**: Compliance requirements, audit trails

### Encryption Best Practices
- Enable default encryption on buckets
- Use SSE-KMS for sensitive data
- Monitor KMS costs for high-volume workloads
- Use bucket policies to enforce encryption

## 🌐 Static Website Hosting

### What is Static Website Hosting?
- **Definition**: Host static websites directly from S3 bucket
- **Supported**: HTML, CSS, JavaScript, images, videos
- **Not Supported**: Server-side processing (PHP, ASP.NET, etc.)

### Website Configuration
- **Index Document**: Default page (e.g., index.html)
- **Error Document**: Custom error page (e.g., error.html)
- **Redirection Rules**: Redirect requests to other pages/sites

### Website URL Format
```
http://bucket-name.s3-website-region.amazonaws.com
http://bucket-name.s3-website.region.amazonaws.com

Example:
http://my-website.s3-website-us-east-1.amazonaws.com
```

### Static Website Requirements
1. Enable static website hosting on bucket
2. Make bucket and objects publicly readable
3. Upload website files (HTML, CSS, JS, images)
4. Configure index and error documents
5. Optionally configure custom domain with Route 53

### Use Cases
- Marketing websites
- Documentation sites
- Single Page Applications (SPAs)
- Portfolio websites
- Landing pages

## 🎯 Best Practices
- **Versioning**: Enable for critical data
- **Lifecycle**: Automate cost optimization
- **Encryption**: Enable default encryption
- **Static Websites**: Use CloudFront for better performance
- **Monitoring**: Set up CloudWatch metrics and alarms