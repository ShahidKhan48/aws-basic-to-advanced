# Cross Account Access & Service Roles

## 🔄 Cross Account Access

### What is Cross Account Access?
- **Definition**: Allow users/services in one AWS account to access resources in another account
- **Purpose**: Enable secure collaboration between different AWS accounts
- **Method**: IAM roles with cross-account trust policies
- **Security**: Maintains account isolation while enabling controlled access

### Cross Account Architecture
```
Account A (Trusted)     →     Account B (Trusting)
├── User/Role          →     ├── Cross-Account Role
├── AssumeRole API     →     ├── Trust Policy
└── Temporary Creds    →     └── Permission Policy
```

### Setting Up Cross Account Access

#### Step 1: Create Role in Target Account (Account B)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT-A-ID:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "unique-external-id"
        }
      }
    }
  ]
}
```

#### Step 2: Grant AssumeRole Permission in Source Account (Account A)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::ACCOUNT-B-ID:role/CrossAccountRole"
    }
  ]
}
```

### Cross Account Use Cases
- **Centralized Logging**: Log aggregation account
- **Security Auditing**: Security team access to all accounts
- **Backup and DR**: Cross-account backup strategies
- **Third-party Access**: Vendor access to specific resources
- **Multi-environment**: Dev/staging/prod account separation

## 🛠️ Service Roles

### What are Service Roles?
- **Definition**: IAM roles that AWS services can assume to perform actions on your behalf
- **Purpose**: Grant AWS services permissions to access other AWS services
- **Security**: Services get only necessary permissions, no long-term credentials
- **Temporary**: Services receive temporary credentials through STS

### Common Service Roles

#### EC2 Instance Roles
- **Purpose**: Allow EC2 instances to access AWS services
- **Mechanism**: Instance profile contains the role
- **Benefits**: No hardcoded credentials, automatic credential rotation

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
      "Resource": "arn:aws:s3:::my-app-bucket/*"
    }
  ]
}
```

#### Lambda Execution Roles
- **Purpose**: Allow Lambda functions to access AWS services
- **Basic Permissions**: CloudWatch Logs for function logging
- **Additional**: Based on function requirements

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem"
      ],
      "Resource": "arn:aws:dynamodb:region:account:table/MyTable"
    }
  ]
}
```

#### ECS Task Roles
- **Purpose**: Allow ECS tasks to access AWS services
- **Granularity**: Different roles for different tasks
- **Security**: Each task gets only required permissions

#### CodePipeline Service Roles
- **Purpose**: Allow CodePipeline to orchestrate deployments
- **Permissions**: Access to CodeCommit, CodeBuild, CodeDeploy, S3

### Service Role Trust Policies

#### EC2 Service Trust Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

#### Lambda Service Trust Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## 🔐 Security Considerations

### External ID
- **Purpose**: Additional security for cross-account access
- **Use Case**: Third-party access, prevent confused deputy problem
- **Implementation**: Unique string known only to you and trusted party

```json
{
  "Condition": {
    "StringEquals": {
      "sts:ExternalId": "unique-external-id-12345"
    }
  }
}
```

### Confused Deputy Problem
- **Problem**: Service acts on behalf of attacker instead of legitimate user
- **Solution**: Use external ID and condition keys
- **Example**: Third-party service accessing wrong customer's resources

### Session Duration
- **Default**: 1 hour for assumed roles
- **Range**: 15 minutes to 12 hours
- **Configuration**: Set in role or during AssumeRole call
- **Security**: Shorter duration reduces risk window

## 🏗️ Advanced Patterns

### Role Chaining
- **Definition**: Assume role from another assumed role
- **Limitation**: Maximum 1 hour session duration
- **Use Case**: Complex multi-account scenarios
- **Security**: Each hop reduces session duration

### Permission Boundaries
- **Purpose**: Set maximum permissions for IAM entities
- **Use Case**: Delegate permission management safely
- **Implementation**: Attach boundary policy to users/roles

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "ec2:Describe*"
      ],
      "Resource": "*"
    }
  ]
}
```

## 🎯 Best Practices

### Cross Account Access
- Use external ID for third-party access
- Implement least privilege principle
- Monitor cross-account role usage
- Regular review of trust relationships
- Use AWS Organizations for account management

### Service Roles
- Create specific roles for each service
- Grant minimum required permissions
- Use AWS managed policies when available
- Regular review and cleanup of unused roles
- Monitor service role usage with CloudTrail

### General Security
- Enable CloudTrail for all accounts
- Use AWS Config for compliance monitoring
- Implement alerting for suspicious activities
- Regular access reviews and audits
- Document role purposes and usage patterns

### Monitoring and Auditing
- CloudTrail for API call logging
- CloudWatch for metrics and alarms
- AWS Config for configuration compliance
- Access Analyzer for permission analysis
- Regular security assessments