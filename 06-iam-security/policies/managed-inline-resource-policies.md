# IAM Policies - Managed, Inline & Resource-based

## 📋 Policy Overview

### What are IAM Policies?
- **Definition**: JSON documents that define permissions
- **Purpose**: Specify what actions are allowed or denied on which resources
- **Language**: AWS Policy Language (JSON format)
- **Evaluation**: Explicit deny always wins, then explicit allow

## 🏢 AWS Managed Policies

### What are AWS Managed Policies?
- **Definition**: Pre-built policies created and maintained by AWS
- **Benefits**: Best practices, automatically updated, version controlled
- **Scope**: Can be attached to multiple users, groups, or roles
- **Updates**: AWS updates them for new services and features

### Common AWS Managed Policies
```
PowerUserAccess: Full access except IAM management
ReadOnlyAccess: Read-only access to all AWS services
AmazonS3FullAccess: Full access to S3
AmazonS3ReadOnlyAccess: Read-only access to S3
AmazonEC2FullAccess: Full access to EC2
IAMReadOnlyAccess: Read-only access to IAM
```

### AWS Managed Policy Benefits
- Maintained by AWS security experts
- Automatically updated for new services
- Version history and rollback capability
- Widely tested and validated
- Follow AWS security best practices

## 👤 Customer Managed Policies

### What are Customer Managed Policies?
- **Definition**: Custom policies created and managed by you
- **Benefits**: Tailored to specific requirements, reusable
- **Versioning**: Support up to 5 versions
- **Scope**: Can be attached to multiple identities

### Customer Managed Policy Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-company-bucket/*",
      "Condition": {
        "StringEquals": {
          "s3:x-amz-server-side-encryption": "AES256"
        }
      }
    }
  ]
}
```

## 📝 Inline Policies

### What are Inline Policies?
- **Definition**: Policies directly embedded in a single user, group, or role
- **Relationship**: One-to-one relationship with identity
- **Lifecycle**: Deleted when identity is deleted
- **Use Case**: Unique permissions for specific identity

### Inline Policy Characteristics
- Cannot be reused across identities
- No versioning support
- Harder to manage at scale
- Good for one-off, specific permissions
- Deleted automatically with identity

### When to Use Inline Policies
- Unique permissions for single identity
- Ensure policy is never accidentally attached elsewhere
- One-time, specific access requirements
- Testing and development scenarios

## 🏗️ Resource-based Policies

### What are Resource-based Policies?
- **Definition**: Policies attached directly to AWS resources
- **Purpose**: Control access to specific resources
- **Principal**: Must specify who gets access
- **Scope**: Apply only to the specific resource

### Resource-based Policy Examples

#### S3 Bucket Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/ExampleUser"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

#### Lambda Resource Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "s3.amazonaws.com"
      },
      "Action": "lambda:InvokeFunction",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:MyFunction"
    }
  ]
}
```

## 🔄 Policy Types Comparison

| Feature | AWS Managed | Customer Managed | Inline | Resource-based |
|---------|-------------|------------------|--------|----------------|
| Reusability | High | High | None | N/A |
| Versioning | Yes | Yes | No | Varies |
| Maintenance | AWS | Customer | Customer | Customer |
| Attachment | Multiple | Multiple | Single | Resource |
| Principal | Implicit | Implicit | Implicit | Explicit |

## 📊 Policy Evaluation Logic

### Evaluation Process
1. **Default**: Implicit deny for all requests
2. **Explicit Deny**: Check for explicit deny (always wins)
3. **Explicit Allow**: Check for explicit allow
4. **Final Decision**: Allow only if explicit allow and no explicit deny

### Policy Evaluation Flow
```
Request → Authentication → Authorization → Resource Policy → Final Decision
                              ↓
                    Identity-based Policies
                    (User, Group, Role policies)
```

## 🎯 Best Practices

### Policy Management
- Use AWS managed policies when possible
- Create customer managed policies for custom requirements
- Use inline policies sparingly for unique cases
- Implement least privilege principle
- Regular policy review and cleanup

### Policy Design
- Use specific actions instead of wildcards
- Implement conditions for additional security
- Use resource ARNs to limit scope
- Document policy purposes
- Test policies before production deployment

### Security Considerations
- Avoid overly permissive policies
- Use conditions to restrict access
- Monitor policy usage with CloudTrail
- Implement policy validation tools
- Regular access reviews and audits

### Naming Conventions
- Use descriptive policy names
- Include purpose and scope in names
- Use consistent naming patterns
- Version control for custom policies
- Document policy changes and reasons