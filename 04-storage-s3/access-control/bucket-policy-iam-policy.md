# S3 Bucket Policy & IAM Policy

## 📋 Bucket Policies

### What are Bucket Policies?
- **Definition**: JSON-based access policy attached to S3 bucket
- **Scope**: Apply to bucket and all objects within it
- **Type**: Resource-based policy
- **Language**: AWS Policy Language (JSON)

### Bucket Policy Structure
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### Bucket Policy Elements
- **Version**: Policy language version
- **Statement**: Array of policy statements
- **Sid**: Statement ID (optional)
- **Effect**: Allow or Deny
- **Principal**: Who the policy applies to
- **Action**: What actions are allowed/denied
- **Resource**: Which resources the policy applies to
- **Condition**: When the policy applies (optional)

### Common Bucket Policy Examples

#### Public Read Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-public-bucket/*"
    }
  ]
}
```

#### Restrict Access by IP
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ],
      "Condition": {
        "NotIpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        }
      }
    }
  ]
}
```

## 👤 IAM Policies for S3

### What are IAM Policies?
- **Definition**: Identity-based policies attached to users, groups, or roles
- **Scope**: Define what actions an identity can perform
- **Type**: Identity-based policy
- **Inheritance**: Users inherit permissions from groups

### IAM Policy Structure
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
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### Common IAM Policy Examples

#### Full S3 Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

#### Read-Only Access to Specific Bucket
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-bucket",
        "arn:aws:s3:::my-bucket/*"
      ]
    }
  ]
}
```

#### Access to Specific Folder
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/user-folder/*"
    }
  ]
}
```

## 🔄 Bucket Policy vs IAM Policy

| Feature | Bucket Policy | IAM Policy |
|---------|---------------|------------|
| Attachment | Attached to bucket | Attached to identity |
| Scope | Bucket and objects | User/group/role permissions |
| Principal | Can specify external principals | Implicit (attached identity) |
| Cross-Account | Easy cross-account access | Requires role assumption |
| Management | Managed per bucket | Centralized identity management |

## 🎯 Best Practices

### Bucket Policies
- Use for cross-account access
- Use for public access scenarios
- Keep policies simple and readable
- Use conditions for additional security
- Regular review and audit

### IAM Policies
- Use for internal user/application access
- Follow principle of least privilege
- Use managed policies when possible
- Group similar permissions
- Use policy simulator for testing

### Security Considerations
- Never use wildcard (*) for sensitive operations
- Use MFA conditions for sensitive actions
- Implement IP restrictions where appropriate
- Enable CloudTrail for access logging
- Regular access reviews and cleanup