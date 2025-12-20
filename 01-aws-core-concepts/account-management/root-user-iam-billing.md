# Root User, IAM User, IAM Role & Billing

## 👑 Root User
- **Definition**: Primary account holder with complete access
- **Characteristics**:
  - Email address used to create AWS account
  - Full access to all services and resources
  - Cannot be restricted by IAM policies
- **Security Best Practices**:
  - Enable MFA immediately
  - Don't use for daily operations
  - Create IAM users for regular tasks
  - Secure credentials in safe location

## 👤 IAM Users
- **Purpose**: Individual identities for people or applications
- **Features**:
  - Unique credentials (username/password or access keys)
  - Granular permissions via policies
  - Can belong to multiple groups
- **Use Cases**:
  - Individual developers
  - Applications requiring AWS access
  - Service accounts
- **Best Practices**:
  - Use groups for permission management
  - Enable MFA for console access
  - Rotate access keys regularly

## 🎭 IAM Roles
- **Definition**: Temporary credentials for AWS services or external identities
- **Benefits**:
  - No permanent credentials stored
  - Automatic credential rotation
  - Cross-account access capability
- **Common Use Cases**:
  - EC2 instances accessing S3
  - Lambda functions calling other services
  - Cross-account resource access
  - Federated access from external identity providers

## 💰 Billing
- **Free Tier**: 12 months of free usage for new accounts
- **Billing Dashboard**: Monitor costs and usage patterns
- **Cost Management Tools**:
  - Cost Explorer: Analyze spending trends
  - Budgets: Set spending alerts
  - Cost Anomaly Detection: Identify unusual spending
- **Organizations**: Centralized billing for multiple accounts
- **Best Practices**:
  - Set up billing alerts
  - Use tags for cost allocation
  - Review monthly bills regularly
  - Implement cost optimization strategies