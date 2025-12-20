# MFA, STS & Least Privilege

## 🔐 Multi-Factor Authentication (MFA)

### What is MFA?
- **Definition**: Additional security layer requiring two or more verification factors
- **Purpose**: Protect against compromised passwords
- **Factors**: Something you know (password) + something you have (device)
- **Requirement**: Can be enforced through IAM policies

### MFA Device Types

#### Virtual MFA Devices
- **Apps**: Google Authenticator, Authy, Microsoft Authenticator
- **Cost**: Free
- **Setup**: Scan QR code with authenticator app
- **Backup**: Multiple devices can be registered

#### Hardware MFA Devices
- **FIDO Security Keys**: YubiKey, SoloKeys
- **Hardware Tokens**: Gemalto, RSA SecurID
- **Cost**: Purchase required
- **Durability**: Physical device, more secure

#### SMS MFA (Not Recommended)
- **Method**: SMS text messages
- **Security**: Less secure due to SIM swapping attacks
- **Use**: Only for non-critical accounts

### MFA Policy Example
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "NotAction": [
        "iam:CreateVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:GetUser",
        "iam:ListMFADevices",
        "iam:ListVirtualMFADevices",
        "iam:ResyncMFADevice",
        "sts:GetSessionToken"
      ],
      "Resource": "*",
      "Condition": {
        "BoolIfExists": {
          "aws:MultiFactorAuthPresent": "false"
        }
      }
    }
  ]
}
```

## 🎫 Security Token Service (STS)

### What is STS?
- **Definition**: Web service for requesting temporary, limited-privilege credentials
- **Purpose**: Enable secure access without long-term credentials
- **Duration**: 15 minutes to 12 hours (default 1 hour)
- **Global**: Available in all AWS regions

### STS Operations

#### AssumeRole
- **Purpose**: Assume an IAM role and receive temporary credentials
- **Use Cases**: Cross-account access, service-to-service access
- **Duration**: Configurable (15 min to 12 hours)

#### AssumeRoleWithWebIdentity
- **Purpose**: Assume role using web identity token
- **Providers**: Amazon, Facebook, Google, any OpenID Connect provider
- **Use Cases**: Mobile apps, web applications

#### AssumeRoleWithSAML
- **Purpose**: Assume role using SAML assertion
- **Use Cases**: Enterprise federation with Active Directory
- **Duration**: Up to 12 hours

#### GetSessionToken
- **Purpose**: Get temporary credentials for existing user
- **MFA**: Can require MFA for additional security
- **Use Cases**: Temporary access, MFA-protected operations

### STS Token Components
- **AccessKeyId**: Temporary access key
- **SecretAccessKey**: Temporary secret key
- **SessionToken**: Required for all API calls
- **Expiration**: When credentials expire

## 🔒 Least Privilege Principle

### What is Least Privilege?
- **Definition**: Grant minimum permissions necessary to perform required tasks
- **Purpose**: Reduce security risk and potential damage from compromised accounts
- **Implementation**: Start with no permissions, add only what's needed
- **Review**: Regular review and removal of unnecessary permissions

### Implementing Least Privilege

#### Start with Deny All
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

#### Add Specific Permissions
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

#### Use Conditions for Restrictions
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "ec2:Region": "us-east-1"
        },
        "ForAllValues:StringEquals": {
          "ec2:InstanceType": ["t2.micro", "t2.small"]
        }
      }
    }
  ]
}
```

### Least Privilege Strategies

#### Time-based Access
- **JIT Access**: Just-in-time access for temporary needs
- **Session Duration**: Limit session duration for roles
- **Scheduled Access**: Time-based access controls

#### Resource-specific Permissions
- **Specific ARNs**: Use specific resource ARNs instead of wildcards
- **Prefix Matching**: Limit access to specific prefixes
- **Tag-based Access**: Use resource tags for access control

#### Action-specific Permissions
- **Granular Actions**: Use specific actions instead of service:*
- **Read vs Write**: Separate read and write permissions
- **Administrative Actions**: Restrict administrative actions

## 🛡️ Security Best Practices

### MFA Best Practices
- Enable MFA for all privileged users
- Use hardware MFA for highly privileged accounts
- Enforce MFA through IAM policies
- Regular MFA device audits
- Backup MFA devices for critical accounts

### STS Best Practices
- Use roles instead of long-term credentials
- Set appropriate session durations
- Monitor STS usage with CloudTrail
- Implement external ID for third-party access
- Use condition keys for additional security

### Least Privilege Best Practices
- Regular access reviews and audits
- Use AWS Access Analyzer for unused permissions
- Implement permission boundaries
- Start with minimal permissions and expand as needed
- Document permission requirements and justifications

## 📊 Monitoring and Auditing

### CloudTrail Integration
- Log all STS API calls
- Monitor role assumptions
- Track MFA usage
- Alert on suspicious activities

### Access Analyzer
- Identify unused permissions
- Find overly permissive policies
- Analyze external access
- Generate least privilege policies

### IAM Credential Reports
- Track password age and usage
- Monitor MFA device status
- Identify inactive users
- Access key rotation tracking