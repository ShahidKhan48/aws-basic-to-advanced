# AMI (Amazon Machine Image)

## 📀 What is AMI?
- **Definition**: Template containing OS, applications, and configurations
- **Purpose**: Launch EC2 instances with pre-configured software
- **Components**:
  - Root volume template (OS and applications)
  - Launch permissions
  - Block device mapping

## 🔧 AMI Types

### AWS AMIs
- **Amazon Linux 2**: AWS-optimized, free tier eligible
- **Ubuntu**: Popular Linux distribution
- **Windows Server**: Microsoft Windows variants
- **Red Hat Enterprise Linux**: Enterprise Linux
- **SUSE Linux**: Enterprise Linux distribution

### Marketplace AMIs
- Third-party software solutions
- Pre-configured applications (WordPress, databases)
- Commercial software with licensing
- Certified by AWS partners

### Community AMIs
- Shared by AWS community
- Free to use
- Various configurations available
- Use with caution (security review needed)

### Custom AMIs
- Created from your configured instances
- Organization-specific configurations
- Standardized deployments
- Version control for infrastructure

## 🛠️ Creating Custom AMI
1. Launch and configure EC2 instance
2. Install required software and configurations
3. Create AMI from running instance
4. Test AMI by launching new instance
5. Share or keep private as needed

## 📋 Best Practices
- Keep AMIs updated with security patches
- Use versioning for AMI management
- Document AMI contents and configurations
- Regular cleanup of unused AMIs
- Encrypt AMIs for sensitive workloads
- Test AMIs before production use