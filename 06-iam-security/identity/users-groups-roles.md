# IAM Users, Groups & Roles

## 👤 IAM Users

### What are IAM Users?
- **Definition**: Individual identities with unique credentials
- **Purpose**: Represent people or applications that need AWS access
- **Credentials**: Username/password for console, access keys for API/CLI
- **Limit**: 5000 users per AWS account

### User Components
- **Username**: Unique identifier within account
- **Password**: For AWS Management Console access
- **Access Keys**: For programmatic access (API, CLI, SDK)
- **Permissions**: Attached policies define what user can do
- **Groups**: Users can belong to multiple groups

### User Best Practices
- Create individual users (don't share credentials)
- Use groups to assign permissions
- Enable MFA for console access
- Rotate access keys regularly
- Use roles instead of users for applications when possible

## 👥 IAM Groups

### What are IAM Groups?
- **Definition**: Collection of IAM users
- **Purpose**: Simplify permission management
- **Inheritance**: Users inherit permissions from all groups they belong to
- **Limit**: 300 groups per AWS account, user can be in 10 groups

### Group Benefits
- **Simplified Management**: Assign permissions to groups, not individual users
- **Consistency**: Ensure users with similar roles have same permissions
- **Scalability**: Easy to add/remove users from groups
- **Audit**: Easier to track permissions by role/function

### Common Group Examples
```
Developers Group:
- EC2 read/write access
- S3 development bucket access
- CloudWatch logs access

Administrators Group:
- Full AWS access
- Billing access
- IAM management

Read-Only Group:
- View-only access to all services
- No modification permissions
```

## 🎭 IAM Roles

### What are IAM Roles?
- **Definition**: Set of permissions that can be assumed by trusted entities
- **Purpose**: Temporary access without permanent credentials
- **Assumption**: Entities assume roles to get temporary credentials
- **Duration**: Configurable session duration (15 minutes to 12 hours)

### Role Components
- **Trust Policy**: Defines who can assume the role
- **Permission Policies**: Define what the role can do
- **Role ARN**: Unique identifier for the role
- **Session Duration**: How long assumed credentials are valid

### Types of Roles

#### Service Roles
- **Purpose**: Allow AWS services to access other services
- **Examples**:
  - EC2 instance role to access S3
  - Lambda execution role to access DynamoDB
  - ECS task role for container permissions

#### Cross-Account Roles
- **Purpose**: Allow access across AWS accounts
- **Use Cases**:
  - Third-party access to your resources
  - Centralized logging account access
  - Multi-account organization management

#### Identity Provider Roles
- **Purpose**: Federated access from external identity providers
- **Examples**:
  - SAML federation with Active Directory
  - Web identity federation with Google/Facebook
  - OIDC federation with GitHub Actions

## 🔄 Role Assumption Process

### How Role Assumption Works
1. **Request**: Entity requests to assume role
2. **Verification**: AWS verifies trust policy
3. **Issue**: AWS issues temporary credentials (STS token)
4. **Use**: Entity uses temporary credentials
5. **Expire**: Credentials expire after session duration

### STS (Security Token Service)
- **AssumeRole**: Assume a role and get temporary credentials
- **AssumeRoleWithWebIdentity**: For web identity federation
- **AssumeRoleWithSAML**: For SAML federation
- **GetSessionToken**: Get temporary credentials for MFA

## 🏗️ Identity Architecture Patterns

### EC2 Instance Roles
```
EC2 Instance → Instance Profile → IAM Role → Permissions
```

### Cross-Account Access
```
Account A User → Assume Role → Account B Role → Account B Resources
```

### Federated Access
```
External User → Identity Provider → SAML/OIDC → IAM Role → AWS Resources
```

## 📋 Permission Inheritance

### User Permissions
- Direct policy attachments
- Group membership policies
- Inline policies

### Role Permissions
- Role permission policies
- Resource-based policies (when applicable)
- Session policies (optional constraints)

## 🎯 Best Practices

### Users
- Create individual users for each person
- Use groups for permission management
- Enable MFA for privileged users
- Regular access review and cleanup
- Use temporary credentials when possible

### Groups
- Organize by job function or project
- Use descriptive names
- Avoid nesting groups (not supported)
- Regular review of group memberships

### Roles
- Use roles for applications and services
- Implement least privilege principle
- Use external ID for third-party access
- Monitor role usage with CloudTrail
- Set appropriate session durations

### General
- Follow principle of least privilege
- Use AWS managed policies when possible
- Regular audit of permissions
- Implement proper naming conventions
- Document role purposes and usage