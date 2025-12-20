# Shared Responsibility Model

## 🏗️ AWS Responsibilities (Security OF the Cloud)
- **Infrastructure**: Physical data centers, hardware, networking
- **Host Operating System**: Hypervisor and host OS patching
- **Network Controls**: Network infrastructure, DDoS protection
- **Virtualization Infrastructure**: Hypervisor isolation
- **Hardware/Software**: Physical security, environmental controls
- **Managed Services**: RDS, Lambda, S3 underlying infrastructure

## 👤 Customer Responsibilities (Security IN the Cloud)
- **Operating System**: Guest OS updates and security patches
- **Applications**: Application code, runtime, middleware
- **Data**: Data encryption, backup, access controls
- **IAM**: User management, permissions, access policies
- **Network Configuration**: Security groups, NACLs, firewall rules
- **Client-side Encryption**: Data encryption before upload

## 📊 Service-Specific Responsibilities

### Infrastructure Services (EC2, VPC)
- **AWS**: Physical infrastructure, hypervisor
- **Customer**: OS, applications, data, network config

### Container Services (ECS, EKS)
- **AWS**: Control plane, infrastructure
- **Customer**: Container images, cluster config, data

### Managed Services (RDS, S3)
- **AWS**: Infrastructure, OS, platform management
- **Customer**: Data, access controls, encryption settings

### Serverless (Lambda, DynamoDB)
- **AWS**: Everything except your code and data
- **Customer**: Code, data, access permissions

## 🔒 Security Best Practices
- Implement defense in depth
- Use principle of least privilege
- Enable logging and monitoring
- Regular security assessments
- Keep systems updated
- Encrypt data in transit and at rest